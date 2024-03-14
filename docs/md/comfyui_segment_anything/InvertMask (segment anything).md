# InvertMask (segment anything)
## Documentation
- Class name: `InvertMask`
- Category: `segment_anything`
- Output node: `False`

The InvertMask node inverts the input mask by subtracting each element from 1.0, effectively flipping the mask's binary values. This operation is useful for switching the focus between the masked and unmasked regions in various image processing tasks.
## Input types
### Required
- **`mask`**
    - The input mask to be inverted. This mask defines areas of interest in an image or another graphical representation. Inverting the mask switches the focus between the foreground and background.
    - Python dtype: `torch.Tensor`
    - Comfy dtype: `MASK`
## Output types
- **`mask`**
    - The inverted mask, with each original value subtracted from 1.0. This output is useful for operations that require the opposite areas of the original mask to be highlighted.
    - Python dtype: `torch.Tensor`
    - Comfy dtype: `MASK`
## Usage tips
- Infra type: `CPU`
- Common nodes: `MaskToImage,PorterDuffImageComposite,ImageCompositeMasked,VAEEncodeForInpaint,FeatherMask`

The InvertMask node inverts the input mask by subtracting each element from 1.0, effectively flipping the mask's binary values. This operation is useful for switching the focus between the masked and unmasked regions in various image processing tasks, often used with segmentation to invert selected areas for operations like inpainting or selective application of effects.
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
    CATEGORY = "segment_anything"
    FUNCTION = "main"
    RETURN_TYPES = ("MASK",)

    def main(self, mask):
        out = 1.0 - mask
        return (out,)

```
