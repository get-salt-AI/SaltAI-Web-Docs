# Split Latent Batch 🎥🅥🅗🅢
## Documentation
- Class name: `VHS_SplitLatents`
- Category: `Video Helper Suite 🎥🅥🅗🅢/latent`
- Output node: `False`

The `VHS_SplitLatents` node is designed to divide a batch of latents into two groups based on a specified index. This operation facilitates the manipulation of latent batches by allowing users to work with subsets of the data.
## Input types
### Required
- **`latents`**
    - Represents the batch of latents to be split. It is crucial for determining the subsets of data to be worked with after the split.
    - Python dtype: `Dict[str, Union[Tensor, List[Tensor], Dict[str, Tensor]]]`
    - Comfy dtype: `LATENT`
- **`split_index`**
    - The index at which the batch of latents is split into two groups. This parameter is essential for defining the boundary between the two resulting subsets.
    - Python dtype: `int`
    - Comfy dtype: `INT`
## Output types
- **`latent`**
    - The second subset of latents obtained after the split, allowing for separate processing or analysis.
    - Python dtype: `Dict[str, Union[Tensor, List[Tensor], Dict[str, Tensor]]]`
    - Comfy dtype: `LATENT`
- **`int`**
    - The count of latents in the second subset, indicating the size of this group.
    - Python dtype: `int`
    - Comfy dtype: `INT`
## Usage tips
- Infra type: `CPU`
- Common nodes: unknown


## Source code
```python
class SplitLatents:
    @classmethod
    def INPUT_TYPES(s):
        return {
                "required": {
                    "latents": ("LATENT",),
                    "split_index": ("INT", {"default": 0, "step": 1, "min": -99999999999}),
                },
            }
    
    CATEGORY = "Video Helper Suite 🎥🅥🅗🅢/latent"

    RETURN_TYPES = ("LATENT", "INT", "LATENT", "INT")
    RETURN_NAMES = ("LATENT_A", "A_count", "LATENT_B", "B_count")
    FUNCTION = "split_latents"

    def split_latents(self, latents: dict, split_index: int):
        latents = latents.copy()
        group_a = latents["samples"][:split_index]
        group_b = latents["samples"][split_index:]
        group_a_latent = {"samples": group_a}
        group_b_latent = {"samples": group_b}
        return (group_a_latent, group_a.size(0), group_b_latent, group_b.size(0))

```
