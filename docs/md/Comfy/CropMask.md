# CropMask
## Documentation
- Class name: `CropMask`
- Category: `mask`
- Output node: `False`

The `CropMask` node is designed to crop a given mask to a specified rectangular area. It reshapes the input mask to ensure compatibility, then extracts the specified region based on the provided coordinates and dimensions.
## Input types
### Required
- **`mask`**
    - The mask to be cropped. It's reshaped to ensure compatibility before cropping.
    - Python dtype: `torch.Tensor`
    - Comfy dtype: `MASK`
- **`x`**
    - The x-coordinate of the top-left corner of the cropping rectangle.
    - Python dtype: `int`
    - Comfy dtype: `INT`
- **`y`**
    - The y-coordinate of the top-left corner of the cropping rectangle.
    - Python dtype: `int`
    - Comfy dtype: `INT`
- **`width`**
    - The width of the cropping rectangle.
    - Python dtype: `int`
    - Comfy dtype: `INT`
- **`height`**
    - The height of the cropping rectangle.
    - Python dtype: `int`
    - Comfy dtype: `INT`
## Output types
- **`mask`**
    - The cropped mask, which is a subsection of the original mask based on the specified coordinates and dimensions.
    - Python dtype: `torch.Tensor`
    - Comfy dtype: `MASK`
## Usage tips
- Infra type: `CPU`
- Common nodes: `VAEEncodeForInpaint,MaskToImage`


## Source code
```python
class CropMask:
    @classmethod
    def INPUT_TYPES(cls):
        return {
            "required": {
                "mask": ("MASK",),
                "x": ("INT", {"default": 0, "min": 0, "max": MAX_RESOLUTION, "step": 1}),
                "y": ("INT", {"default": 0, "min": 0, "max": MAX_RESOLUTION, "step": 1}),
                "width": ("INT", {"default": 512, "min": 1, "max": MAX_RESOLUTION, "step": 1}),
                "height": ("INT", {"default": 512, "min": 1, "max": MAX_RESOLUTION, "step": 1}),
            }
        }

    CATEGORY = "mask"

    RETURN_TYPES = ("MASK",)

    FUNCTION = "crop"

    def crop(self, mask, x, y, width, height):
        mask = mask.reshape((-1, mask.shape[-2], mask.shape[-1]))
        out = mask[:, y:y + height, x:x + width]
        return (out,)

```
