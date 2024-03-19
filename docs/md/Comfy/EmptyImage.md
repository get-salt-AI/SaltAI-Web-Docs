# EmptyImage
## Documentation
- Class name: `EmptyImage`
- Category: `image`
- Output node: `False`

The `EmptyImage` node generates a blank image with specified dimensions, batch size, and color. It utilizes PyTorch tensors to create an image filled with the given color.
## Input types
### Required
- **`width`**
    - Specifies the width of the generated image. It directly influences the dimensions of the output image.
    - Python dtype: `int`
    - Comfy dtype: `INT`
- **`height`**
    - Determines the height of the generated image, affecting its dimensions.
    - Python dtype: `int`
    - Comfy dtype: `INT`
- **`batch_size`**
    - Indicates the number of images to generate in a single batch. Affects the first dimension of the output tensor.
    - Python dtype: `int`
    - Comfy dtype: `INT`
- **`color`**
    - Defines the color of the generated image using a hexadecimal RGB value. This parameter sets the overall color tone of the image.
    - Python dtype: `int`
    - Comfy dtype: `INT`
## Output types
- **`image`**
    - The output is a tensor representing a batch of images filled with the specified color.
    - Python dtype: `torch.Tensor`
    - Comfy dtype: `IMAGE`
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
