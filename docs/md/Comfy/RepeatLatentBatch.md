# Repeat Latent Batch
## Documentation
- Class name: `RepeatLatentBatch`
- Category: `latent/batch`
- Output node: `False`

This node is designed to repeat the latent representations of samples a specified number of times. It can also handle repeating associated noise masks and adjusting batch indices accordingly, ensuring the repeated latents maintain their batch structure.
## Input types
### Required
- **`samples`**
    - The latent representations to be repeated. This input is crucial for generating multiple copies of the same latent structure, which can be useful in various generative tasks.
    - Python dtype: `Dict[str, torch.Tensor]`
    - Comfy dtype: `LATENT`
- **`amount`**
    - Specifies how many times the input samples should be repeated. This parameter directly influences the size of the output latent batch, allowing for flexible batch size adjustments.
    - Python dtype: `int`
    - Comfy dtype: `INT`
## Output types
- **`latent`**
    - The output is a modified version of the input latent representations, repeated the specified number of times along with any associated noise masks and batch indices.
    - Python dtype: `Tuple[Dict[str, torch.Tensor]]`
    - Comfy dtype: `LATENT`
## Usage tips
- Infra type: `GPU`
- Common nodes: `KSamplerAdvanced`


## Source code
```python
class RepeatLatentBatch:
    @classmethod
    def INPUT_TYPES(s):
        return {"required": { "samples": ("LATENT",),
                              "amount": ("INT", {"default": 1, "min": 1, "max": 64}),
                              }}
    RETURN_TYPES = ("LATENT",)
    FUNCTION = "repeat"

    CATEGORY = "latent/batch"

    def repeat(self, samples, amount):
        s = samples.copy()
        s_in = samples["samples"]
        
        s["samples"] = s_in.repeat((amount, 1,1,1))
        if "noise_mask" in samples and samples["noise_mask"].shape[0] > 1:
            masks = samples["noise_mask"]
            if masks.shape[0] < s_in.shape[0]:
                masks = masks.repeat(math.ceil(s_in.shape[0] / masks.shape[0]), 1, 1, 1)[:s_in.shape[0]]
            s["noise_mask"] = samples["noise_mask"].repeat((amount, 1,1,1))
        if "batch_index" in s:
            offset = max(s["batch_index"]) - min(s["batch_index"]) + 1
            s["batch_index"] = s["batch_index"] + [x + (i * offset) for i in range(1, amount) for x in s["batch_index"]]
        return (s,)

```
