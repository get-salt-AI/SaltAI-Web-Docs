# Get Latent Count 🎥🅥🅗🅢
## Documentation
- Class name: `VHS_GetLatentCount`
- Category: `Video Helper Suite 🎥🅥🅗🅢/latent`
- Output node: `False`

This node counts the number of latent samples in a given batch. It's useful for understanding the size of the dataset or batch being worked with.
## Input types
### Required
- **`latents`**
    - The input latent samples for which the count is to be determined. This parameter is crucial for the operation as it directly influences the result by providing the dataset to be counted.
    - Python dtype: `dict`
    - Comfy dtype: `LATENT`
## Output types
- **`int`**
    - The total number of latent samples present in the input batch. This output is essential for batch size management and further processing steps.
    - Python dtype: `int`
    - Comfy dtype: `INT`
## Usage tips
- Infra type: `CPU`
- Common nodes: unknown


## Source code
```python
class GetLatentCount:
    @classmethod
    def INPUT_TYPES(s):
        return {
            "required": {
                "latents": ("LATENT",),
            }
        }
    
    CATEGORY = "Video Helper Suite 🎥🅥🅗🅢/latent"

    RETURN_TYPES = ("INT",)
    RETURN_NAMES = ("count",)
    FUNCTION = "count_input"

    def count_input(self, latents: dict):
        return (latents["samples"].size(0),)

```
