# Duplicate Mask Batch 🎥🅥🅗🅢
## Documentation
- Class name: `VHS_DuplicateMasks`
- Category: `Video Helper Suite 🎥🅥🅗🅢/mask`
- Output node: `False`

The `VHS_DuplicateMasks` node duplicates a given mask tensor a specified number of times. This operation is useful for creating multiple copies of the same mask, which can be beneficial in various image processing or machine learning tasks where the same mask needs to be applied to multiple images or layers.
## Input types
### Required
- **`mask`**
    - The mask tensor to be duplicated. This tensor represents a mask that can be applied to images or used in other operations requiring a mask.
    - Python dtype: `torch.Tensor`
    - Comfy dtype: `MASK`
- **`multiply_by`**
    - Specifies the number of times the input mask should be duplicated. This allows for the creation of multiple copies of the same mask, which can be used in subsequent operations.
    - Python dtype: `int`
    - Comfy dtype: `INT`
## Output types
- **`mask`**
    - The duplicated mask tensor, containing multiple copies of the original mask concatenated along the batch dimension.
    - Python dtype: `torch.Tensor`
    - Comfy dtype: `MASK`
- **`int`**
    - The total number of masks in the duplicated mask tensor, which corresponds to the specified multiplication factor.
    - Python dtype: `int`
    - Comfy dtype: `INT`
## Usage tips
- Infra type: `GPU`
- Common nodes: unknown


## Source code
```python
class DuplicateMasks:
    @classmethod
    def INPUT_TYPES(s):
        return {
            "required": {
                "mask": ("MASK",),
                "multiply_by": ("INT", {"default": 1, "min": 1, "step": 1})
            }
        }
    
    CATEGORY = "Video Helper Suite 🎥🅥🅗🅢/mask"

    RETURN_TYPES = ("MASK", "INT",)
    RETURN_NAMES = ("MASK", "count",)
    FUNCTION = "duplicate_input"

    def duplicate_input(self, mask: Tensor, multiply_by: int):
        full_masks = []
        for n in range(0, multiply_by):
            full_masks.append(mask)
        new_mask = torch.cat(full_masks, dim=0)
        return (new_mask, new_mask.size(0),)

```
