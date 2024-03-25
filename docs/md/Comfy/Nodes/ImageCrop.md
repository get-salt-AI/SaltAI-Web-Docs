# ImageCrop
## Documentation
- Class name: `ImageCrop`
- Category: `image/transform`
- Output node: `False`

The ImageCrop node is designed for cropping images to a specified width and height starting from a given x and y coordinate. This functionality is essential for focusing on specific regions of an image or for adjusting the image size to meet certain requirements.
## Input types
### Required
- **`image`**
    - Comfy dtype: `IMAGE`
    - The input image to be cropped. This parameter is crucial as it defines the source image from which a region will be extracted based on the specified dimensions and coordinates.
    - Python dtype: `torch.Tensor`
- **`width`**
    - Comfy dtype: `INT`
    - Specifies the width of the cropped image. This parameter determines how wide the resulting cropped image will be.
    - Python dtype: `int`
- **`height`**
    - Comfy dtype: `INT`
    - Specifies the height of the cropped image. This parameter determines the height of the resulting cropped image.
    - Python dtype: `int`
- **`x`**
    - Comfy dtype: `INT`
    - The x-coordinate of the top-left corner of the cropping area. This parameter sets the starting point for the width dimension of the crop.
    - Python dtype: `int`
- **`y`**
    - Comfy dtype: `INT`
    - The y-coordinate of the top-left corner of the cropping area. This parameter sets the starting point for the height dimension of the crop.
    - Python dtype: `int`
## Output types
- **`image`**
    - Comfy dtype: `IMAGE`
    - The cropped image as a result of the cropping operation. This output is significant for further processing or analysis of the specified image region.
    - Python dtype: `torch.Tensor`
## Usage tips
- Infra type: `GPU`
- Common nodes: `VAEEncodeForInpaint,PreviewImage`


## Source code
```python
class ImageCrop:
    @classmethod
    def INPUT_TYPES(s):
        return {"required": { "image": ("IMAGE",),
                              "width": ("INT", {"default": 512, "min": 1, "max": MAX_RESOLUTION, "step": 1}),
                              "height": ("INT", {"default": 512, "min": 1, "max": MAX_RESOLUTION, "step": 1}),
                              "x": ("INT", {"default": 0, "min": 0, "max": MAX_RESOLUTION, "step": 1}),
                              "y": ("INT", {"default": 0, "min": 0, "max": MAX_RESOLUTION, "step": 1}),
                              }}
    RETURN_TYPES = ("IMAGE",)
    FUNCTION = "crop"

    CATEGORY = "image/transform"

    def crop(self, image, width, height, x, y):
        x = min(x, image.shape[2] - 1)
        y = min(y, image.shape[1] - 1)
        to_x = width + x
        to_y = height + y
        img = image[:,y:to_y, x:to_x, :]
        return (img,)

```
