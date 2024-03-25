# EmptyImage
## Documentation
- Class name: `EmptyImage`
- Category: `image`
- Output node: `False`

The EmptyImage node is designed to generate blank images of specified dimensions and color. It allows for the creation of uniform color images that can serve as backgrounds or placeholders in various image processing tasks.
## Input types
### Required
- **`width`**
    - Comfy dtype: `INT`
    - Specifies the width of the generated image. It determines how wide the image will be.
    - Python dtype: `int`
- **`height`**
    - Comfy dtype: `INT`
    - Determines the height of the generated image. It affects the vertical size of the image.
    - Python dtype: `int`
- **`batch_size`**
    - Comfy dtype: `INT`
    - Indicates the number of images to generate in a single batch. This allows for the creation of multiple images at once.
    - Python dtype: `int`
- **`color`**
    - Comfy dtype: `INT`
    - Defines the color of the generated image using a hexadecimal value, allowing for customization of the image's appearance. This parameter enables the selection of a wide range of colors.
    - Python dtype: `torch.Tensor`
## Output types
- **`image`**
    - Comfy dtype: `IMAGE`
    - The output is a tensor representing the generated image or images, with the specified dimensions and color.
    - Python dtype: `torch.Tensor`
## Usage tips
- Infra type: `GPU`
- Common nodes: `ImageCompositeMasked`


## Source code
```python
class EmptyImage:
    def __init__(self, device="cpu"):
        self.device = device

    @classmethod
    def INPUT_TYPES(s):
        return {"required": { "width": ("INT", {"default": 512, "min": 1, "max": MAX_RESOLUTION, "step": 1}),
                              "height": ("INT", {"default": 512, "min": 1, "max": MAX_RESOLUTION, "step": 1}),
                              "batch_size": ("INT", {"default": 1, "min": 1, "max": 4096}),
                              "color": ("INT", {"default": 0, "min": 0, "max": 0xFFFFFF, "step": 1, "display": "color"}),
                              }}
    RETURN_TYPES = ("IMAGE",)
    FUNCTION = "generate"

    CATEGORY = "image"

    def generate(self, width, height, batch_size=1, color=0):
        r = torch.full([batch_size, height, width, 1], ((color >> 16) & 0xFF) / 0xFF)
        g = torch.full([batch_size, height, width, 1], ((color >> 8) & 0xFF) / 0xFF)
        b = torch.full([batch_size, height, width, 1], ((color) & 0xFF) / 0xFF)
        return (torch.cat((r, g, b), dim=-1), )

```
