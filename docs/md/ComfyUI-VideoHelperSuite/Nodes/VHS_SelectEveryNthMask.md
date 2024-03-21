# Select Every Nth Mask 🎥🅥🅗🅢
## Documentation
- Class name: `VHS_SelectEveryNthMask`
- Category: `Video Helper Suite 🎥🅥🅗🅢/mask`
- Output node: `False`

This node selects every Nth mask from a batch of masks, allowing for the thinning of the mask collection based on a specified interval. This can be useful for reducing the number of masks to process or to select specific masks for further operations.
## Input types
### Required
- **`mask`**
    - The input batch of masks from which every Nth mask will be selected. This parameter is crucial for determining the subset of masks to be processed or analyzed further.
    - Python dtype: `torch.Tensor`
    - Comfy dtype: `MASK`
- **`select_every_nth`**
    - Determines the interval at which masks are selected from the batch. A value of 1 means every mask is selected, while higher values thin out the selection, picking every Nth mask.
    - Python dtype: `int`
    - Comfy dtype: `INT`
## Output types
- **`mask`**
    - The subset of masks selected from the input batch, based on the specified interval.
    - Python dtype: `torch.Tensor`
    - Comfy dtype: `MASK`
- **`int`**
    - The total number of masks selected and returned by the node.
    - Python dtype: `int`
    - Comfy dtype: `INT`
## Usage tips
- Infra type: `CPU`
- Common nodes: unknown


## Source code
```python
class SelectEveryNthMask:
    @classmethod
    def INPUT_TYPES(s):
        return {
                "required": {
                    "mask": ("MASK",),
                    "select_every_nth": ("INT", {"default": 1, "min": 1, "step": 1}),
                },
            }
    
    CATEGORY = "Video Helper Suite 🎥🅥🅗🅢/mask"

    RETURN_TYPES = ("MASK", "INT",)
    RETURN_NAMES = ("MASK", "count",)
    FUNCTION = "select_masks"

    def select_masks(self, mask: Tensor, select_every_nth: int):
        sub_mask = mask[0::select_every_nth]
        return (sub_mask, sub_mask.size(0))

```
