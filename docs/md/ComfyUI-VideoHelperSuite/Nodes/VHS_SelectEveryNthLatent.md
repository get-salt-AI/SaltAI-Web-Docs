# Select Every Nth Latent 🎥🅥🅗🅢
## Documentation
- Class name: `VHS_SelectEveryNthLatent`
- Category: `Video Helper Suite 🎥🅥🅗🅢/latent`
- Output node: `False`

This node selects every Nth latent vector from a batch of latents based on the specified interval, effectively thinning the batch. This operation can be useful for reducing the size of the data to be processed or for selecting specific latents at regular intervals for analysis or visualization.
## Input types
### Required
- **`latents`**
    - The batch of latent vectors to be processed. This input is crucial for determining which latents will be selected based on the interval.
    - Python dtype: `dict[str, torch.Tensor]`
    - Comfy dtype: `LATENT`
- **`select_every_nth`**
    - The interval at which latents are selected from the batch. A smaller value results in more latents being selected, while a larger value thins the batch more aggressively.
    - Python dtype: `int`
    - Comfy dtype: `INT`
## Output types
- **`latent`**
    - The thinned batch of latent vectors, containing only every Nth latent based on the specified interval.
    - Python dtype: `dict[str, torch.Tensor]`
    - Comfy dtype: `LATENT`
- **`int`**
    - The total number of latents in the thinned batch, providing a quick way to assess the result of the selection process.
    - Python dtype: `int`
    - Comfy dtype: `INT`
## Usage tips
- Infra type: `CPU`
- Common nodes: unknown


## Source code
```python
class SelectEveryNthLatent:
    @classmethod
    def INPUT_TYPES(s):
        return {
                "required": {
                    "latents": ("LATENT",),
                    "select_every_nth": ("INT", {"default": 1, "min": 1, "step": 1}),
                },
            }
    
    CATEGORY = "Video Helper Suite 🎥🅥🅗🅢/latent"

    RETURN_TYPES = ("LATENT", "INT",)
    RETURN_NAMES = ("LATENT", "count",)
    FUNCTION = "select_latents"

    def select_latents(self, latents: dict, select_every_nth: int):
        sub_latents = latents.copy()["samples"][0::select_every_nth]
        return ({"samples": sub_latents}, sub_latents.size(0))

```
