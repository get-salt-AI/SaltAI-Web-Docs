# Bitwise(MASK - MASK)
## Documentation
- Class name: `SubtractMask`
- Category: `ImpactPack/Operation`
- Output node: `False`

The SubtractMask node performs a subtraction operation between two input masks. It utilizes the `subtract_masks` function to subtract one mask from another, ensuring the operation is only performed if the masks have compatible shapes. If the shapes are incompatible, the first mask is returned unchanged. This operation is useful for removing specific areas or features from a mask based on another mask.
## Input types
### Required
- **`mask1`**
    - The first input mask to be subtracted from. It serves as the base mask for the subtraction operation.
    - Python dtype: `torch.Tensor`
    - Comfy dtype: `MASK`
- **`mask2`**
    - The second input mask to subtract from the first mask. This mask specifies the areas or features to be removed from the first mask.
    - Python dtype: `torch.Tensor`
    - Comfy dtype: `MASK`
## Output types
- **`mask`**
    - The resulting mask after subtracting the second mask from the first. If the masks have incompatible shapes, the first mask is returned unchanged.
    - Python dtype: `torch.Tensor`
    - Comfy dtype: `MASK`
## Usage tips
- Infra type: `CPU`
- Common nodes: unknown


## Source code
```python
class SubtractMask:
    @classmethod
    def INPUT_TYPES(s):
        return {"required": {
                        "mask1": ("MASK", ),
                        "mask2": ("MASK", ),
                      }
                }
    
    RETURN_TYPES = ("MASK",)
    FUNCTION = "doit"

    CATEGORY = "ImpactPack/Operation"

    def doit(self, mask1, mask2):
        mask = subtract_masks(mask1, mask2)
        return (mask,)

```
