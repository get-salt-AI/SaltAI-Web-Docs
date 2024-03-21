# Bitwise(MASK + MASK)
## Documentation
- Class name: `AddMask`
- Category: `ImpactPack/Operation`
- Output node: `False`

The `AddMask` node combines two input masks into a single mask by adding them together. This operation is useful in scenarios where the effects or features of two masks need to be merged into one, enhancing or expanding the mask coverage.
## Input types
### Required
- **`mask1`**
    - The first input mask to be combined. It plays an equal role with `mask2` in the addition operation, contributing to the final merged mask.
    - Python dtype: `torch.Tensor`
    - Comfy dtype: `MASK`
- **`mask2`**
    - The second input mask to be combined with `mask1`. This mask equally contributes to the outcome of the merged mask, ensuring that features from both masks are represented in the final result.
    - Python dtype: `torch.Tensor`
    - Comfy dtype: `MASK`
## Output types
- **`mask`**
    - The result of adding `mask1` and `mask2` together, producing a new mask that combines the features of both input masks.
    - Python dtype: `torch.Tensor`
    - Comfy dtype: `MASK`
## Usage tips
- Infra type: `CPU`
- Common nodes: `InvertMask`


## Source code
```python
class AddMask:
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
        mask = add_masks(mask1, mask2)
        return (mask,)

```
