# Duplicate Latent Batch 🎥🅥🅗🅢
## Documentation
- Class name: `VHS_DuplicateLatents`
- Category: `Video Helper Suite 🎥🅥🅗🅢/latent`
- Output node: `False`

The `VHS_DuplicateLatents` node duplicates a batch of latent representations a specified number of times. This operation can be useful for creating larger datasets from a smaller set of unique latents or for data augmentation purposes.
## Input types
### Required
- **`latents`**
    - The input latent representations to be duplicated. This parameter is crucial for defining the data that will undergo duplication.
    - Python dtype: `dict[str, torch.Tensor]`
    - Comfy dtype: `LATENT`
- **`multiply_by`**
    - Specifies the number of times the input latents should be duplicated. This parameter directly influences the size of the output dataset.
    - Python dtype: `int`
    - Comfy dtype: `INT`
## Output types
- **`latent`**
    - The duplicated latent representations.
    - Python dtype: `dict[str, torch.Tensor]`
    - Comfy dtype: `LATENT`
- **`int`**
    - The total number of latent representations after duplication.
    - Python dtype: `int`
    - Comfy dtype: `INT`
## Usage tips
- Infra type: `GPU`
- Common nodes: `KSampler,LatentComposite`


## Source code
```python
class DuplicateLatents:
    @classmethod
    def INPUT_TYPES(s):
        return {
            "required": {
                "latents": ("LATENT",),
                "multiply_by": ("INT", {"default": 1, "min": 1, "step": 1})
            }
        }
    
    CATEGORY = "Video Helper Suite 🎥🅥🅗🅢/latent"

    RETURN_TYPES = ("LATENT", "INT",)
    RETURN_NAMES = ("LATENT", "count",)
    FUNCTION = "duplicate_input"

    def duplicate_input(self, latents: dict[str, Tensor], multiply_by: int):
        new_latents = latents.copy()
        full_latents = []
        for n in range(0, multiply_by):
            full_latents.append(new_latents["samples"])
        new_latents["samples"] = torch.cat(full_latents, dim=0)
        return (new_latents, new_latents["samples"].size(0),)

```
