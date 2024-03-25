# InvertMask
## Documentation
- Class name: `InvertMask`
- Category: `mask`
- Output node: `False`

The InvertMask node is designed to invert the values of a given mask, effectively flipping the masked and unmasked areas. This operation is fundamental in image processing tasks where the focus of interest needs to be switched between the foreground and the background.
## Input types
### Required
- **`mask`**
    - Comfy dtype: `MASK`
    - The 'mask' parameter represents the input mask to be inverted. It is crucial for determining the areas to be flipped in the inversion process.
    - Python dtype: `torch.Tensor`
## Output types
- **`mask`**
    - Comfy dtype: `MASK`
    - The output is an inverted version of the input mask, with previously masked areas becoming unmasked and vice versa.
    - Python dtype: `torch.Tensor`
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
