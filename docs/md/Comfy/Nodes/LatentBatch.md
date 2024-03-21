# LatentBatch
## Documentation
- Class name: `LatentBatch`
- Category: `latent/batch`
- Output node: `False`

The `LatentBatch` node combines two sets of latent representations (samples) into a single batch. It ensures that the dimensions of the two input samples match, potentially resizing one of them, before concatenating them along the batch dimension. Additionally, it manages batch indices for the combined samples.
## Input types
### Required
- **`samples1`**
    - The first set of latent representations to be combined. It is crucial for constructing the output batch by providing the initial part of the combined samples.
    - Python dtype: `Dict[str, torch.Tensor]`
    - Comfy dtype: `LATENT`
- **`samples2`**
    - The second set of latent representations to be combined with the first. It may be resized to match the dimensions of `samples1` before concatenation, contributing to the latter part of the combined samples.
    - Python dtype: `Dict[str, torch.Tensor]`
    - Comfy dtype: `LATENT`
## Output types
- **`latent`**
    - The combined set of latent representations from `samples1` and `samples2`, concatenated along the batch dimension. It includes updated batch indices reflecting the new combined batch.
    - Python dtype: `Tuple[Dict[str, torch.Tensor]]`
    - Comfy dtype: `LATENT`
## Usage tips
- Infra type: `GPU`
- Common nodes: unknown


## Source code
```python
class LatentBatch:
    @classmethod
    def INPUT_TYPES(s):
        return {"required": { "samples1": ("LATENT",), "samples2": ("LATENT",)}}

    RETURN_TYPES = ("LATENT",)
    FUNCTION = "batch"

    CATEGORY = "latent/batch"

    def batch(self, samples1, samples2):
        samples_out = samples1.copy()
        s1 = samples1["samples"]
        s2 = samples2["samples"]

        if s1.shape[1:] != s2.shape[1:]:
            s2 = comfy.utils.common_upscale(s2, s1.shape[3], s1.shape[2], "bilinear", "center")
        s = torch.cat((s1, s2), dim=0)
        samples_out["samples"] = s
        samples_out["batch_index"] = samples1.get("batch_index", [x for x in range(0, s1.shape[0])]) + samples2.get("batch_index", [x for x in range(0, s2.shape[0])])
        return (samples_out,)

```
