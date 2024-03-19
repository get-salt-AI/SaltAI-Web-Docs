# ImageColorToMask
## Documentation
- Class name: `ImageColorToMask`
- Category: `mask`
- Output node: `False`

This node converts an image to a mask based on a specified color. It first normalizes the image's color values, then creates a mask by comparing each pixel's color to the specified color, resulting in a binary mask where matching pixels are marked.
## Input types
### Required
- **`image`**
    - The input image to be converted into a mask. The image is normalized and compared against the specified color to generate the mask.
    - Python dtype: `torch.Tensor`
    - Comfy dtype: `IMAGE`
- **`color`**
    - The target color used to generate the mask. Pixels in the image that match this color will be included in the mask.
    - Python dtype: `int`
    - Comfy dtype: `INT`
## Output types
- **`mask`**
    - The output binary mask generated from the input image, where pixels matching the specified color are marked.
    - Python dtype: `torch.Tensor`
    - Comfy dtype: `MASK`
## Usage tips
- Infra type: `GPU`
- Common nodes: unknown


## Source code
```python
class ImageColorToMask:
    @classmethod
    def INPUT_TYPES(s):
        return {
                "required": {
                    "image": ("IMAGE",),
                    "color": ("INT", {"default": 0, "min": 0, "max": 0xFFFFFF, "step": 1, "display": "color"}),
                }
        }

    CATEGORY = "mask"

    RETURN_TYPES = ("MASK",)
    FUNCTION = "image_to_mask"

    def image_to_mask(self, image, color):
        temp = (torch.clamp(image, 0, 1.0) * 255.0).round().to(torch.int)
        temp = torch.bitwise_left_shift(temp[:,:,:,0], 16) + torch.bitwise_left_shift(temp[:,:,:,1], 8) + temp[:,:,:,2]
        mask = torch.where(temp == color, 255, 0).float()
        return (mask,)

```
