# Bitwise(MASK & MASK)
## Documentation
- Class name: `BitwiseAndMask`
- Category: `ImpactPack/Operation`
- Output node: `False`

The `BitwiseAndMask` node performs a bitwise AND operation between two masks. This operation is useful for combining masks where the overlapping areas are retained, and non-overlapping areas are discarded.
## Input types
### Required
- **`mask1`**
    - The first input mask for the bitwise AND operation. It plays a crucial role in determining the areas to be retained or discarded in the final mask.
    - Python dtype: `torch.Tensor`
    - Comfy dtype: `MASK`
- **`mask2`**
    - The second input mask for the bitwise AND operation. It equally contributes to the final outcome by specifying which areas of the first mask should be retained.
    - Python dtype: `torch.Tensor`
    - Comfy dtype: `MASK`
## Output types
- **`mask`**
    - The result of the bitwise AND operation between the two input masks, retaining only the overlapping areas.
    - Python dtype: `torch.Tensor`
    - Comfy dtype: `MASK`
## Usage tips
- Infra type: `CPU`
- Common nodes: unknown


## Source code
```python
class BitwiseAndMask:
    @classmethod
    def INPUT_TYPES(s):
        return {"required": {
                        "mask1": ("MASK",),
                        "mask2": ("MASK",),
                    }
                }

    RETURN_TYPES = ("MASK",)
    FUNCTION = "doit"

    CATEGORY = "ImpactPack/Operation"

    def doit(self, mask1, mask2):
        mask = bitwise_and_masks(mask1, mask2)
        return (mask,)

```
