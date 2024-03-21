# InvertMask
## Documentation
- Class name: `InvertMask`
- Category: `mask`
- Output node: `False`

The `InvertMask` node inverts the values of a given mask, effectively transforming the masked areas into unmasked areas and vice versa. This operation is useful in scenarios where the focus of interest in an image needs to be reversed.
## Input types
### Required
- **`mask`**
    - The input mask to be inverted. This mask defines areas of interest that will be transformed to their opposite values.
    - Python dtype: `torch.Tensor`
    - Comfy dtype: `MASK`
## Output types
- **`mask`**
    - The inverted mask, where the originally masked areas are now unmasked, and the originally unmasked areas are now masked.
    - Python dtype: `torch.Tensor`
    - Comfy dtype: `MASK`
## Usage tips
- Infra type: `CPU`
- Common nodes: `MaskToImage,PorterDuffImageComposite,ImageCompositeMasked,VAEEncodeForInpaint,FeatherMask`


## Source code
```python
class InvertMask:
    @classmethod
    def INPUT_TYPES(cls):
        return {
            "required": {
                "mask": ("MASK",),
            }
        }

    CATEGORY = "mask"

    RETURN_TYPES = ("MASK",)

    FUNCTION = "invert"

    def invert(self, mask):
        out = 1.0 - mask
        return (out,)

```
