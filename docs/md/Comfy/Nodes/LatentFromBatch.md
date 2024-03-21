# Latent From Batch
## Documentation
- Class name: `LatentFromBatch`
- Category: `latent/batch`
- Output node: `False`

This node extracts a specific batch of samples from a larger batch of latent representations based on the provided batch index and length. It supports handling of noise masks associated with the samples, ensuring that the extracted batch retains the corresponding noise masks.
## Input types
### Required
- **`samples`**
    - The latent representations from which a specific batch is to be extracted. This parameter is crucial for selecting the subset of data to work with.
    - Python dtype: `Dict[str, torch.Tensor]`
    - Comfy dtype: `LATENT`
- **`batch_index`**
    - The index of the first sample in the batch to be extracted. This parameter determines the starting point of the subset.
    - Python dtype: `int`
    - Comfy dtype: `INT`
- **`length`**
    - The number of samples to extract from the batch, starting from the batch index. This parameter defines the size of the subset.
    - Python dtype: `int`
    - Comfy dtype: `INT`
## Output types
- **`latent`**
    - The extracted batch of latent representations, including any associated noise masks and batch indices.
    - Python dtype: `Tuple[Dict[str, torch.Tensor]]`
    - Comfy dtype: `LATENT`
## Usage tips
- Infra type: `CPU`
- Common nodes: `LatentBlend`


## Source code
```python
class LatentFromBatch:
    @classmethod
    def INPUT_TYPES(s):
        return {"required": { "samples": ("LATENT",),
                              "batch_index": ("INT", {"default": 0, "min": 0, "max": 63}),
                              "length": ("INT", {"default": 1, "min": 1, "max": 64}),
                              }}
    RETURN_TYPES = ("LATENT",)
    FUNCTION = "frombatch"

    CATEGORY = "latent/batch"

    def frombatch(self, samples, batch_index, length):
        s = samples.copy()
        s_in = samples["samples"]
        batch_index = min(s_in.shape[0] - 1, batch_index)
        length = min(s_in.shape[0] - batch_index, length)
        s["samples"] = s_in[batch_index:batch_index + length].clone()
        if "noise_mask" in samples:
            masks = samples["noise_mask"]
            if masks.shape[0] == 1:
                s["noise_mask"] = masks.clone()
            else:
                if masks.shape[0] < s_in.shape[0]:
                    masks = masks.repeat(math.ceil(s_in.shape[0] / masks.shape[0]), 1, 1, 1)[:s_in.shape[0]]
                s["noise_mask"] = masks[batch_index:batch_index + length].clone()
        if "batch_index" not in s:
            s["batch_index"] = [x for x in range(batch_index, batch_index+length)]
        else:
            s["batch_index"] = samples["batch_index"][batch_index:batch_index + length]
        return (s,)

```
