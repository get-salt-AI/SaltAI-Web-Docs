# Invert Image
## Documentation
- Class name: `ImageInvert`
- Category: `image`
- Output node: `False`

The `ImageInvert` node inverts the colors of an input image by subtracting each color value from 1. This operation effectively reverses the color scheme of the image, turning light areas dark and vice versa.
## Input types
### Required
- **`image`**
    - The input image to be inverted. The inversion process changes the color values of the image to their complementary values, affecting the overall appearance.
    - Python dtype: `torch.Tensor`
    - Comfy dtype: `IMAGE`
## Output types
- **`image`**
    - The inverted image with its colors reversed. This output is useful for creating visual contrasts or for preprocessing steps in image processing pipelines.
    - Python dtype: `torch.Tensor`
    - Comfy dtype: `IMAGE`
## Usage tips
- Infra type: `GPU`
- Common nodes: `PreviewImage,ImageScale,ImageCompositeMasked,Reroute,ArithmeticBlend`


## Source code
```python
class ImageInvert:

    @classmethod
    def INPUT_TYPES(s):
        return {"required": { "image": ("IMAGE",)}}

    RETURN_TYPES = ("IMAGE",)
    FUNCTION = "invert"

    CATEGORY = "image"

    def invert(self, image):
        s = 1.0 - image
        return (s,)

```
