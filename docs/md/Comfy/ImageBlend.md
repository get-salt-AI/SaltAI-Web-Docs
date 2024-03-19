# ImageBlend
## Documentation
- Class name: `ImageBlend`
- Category: `image/postprocessing`
- Output node: `False`

The `ImageBlend` node blends two images together based on a specified blend mode and blend factor. It supports various blend modes such as normal, multiply, screen, overlay, soft light, and difference. The blend factor determines the weight of each image in the final blend, allowing for a seamless mix of the two images. This node is useful for image post-processing tasks where combining images with different effects is required.
## Input types
### Required
- **`image1`**
    - The first image to be blended. It serves as the base layer onto which the second image is blended.
    - Python dtype: `torch.Tensor`
    - Comfy dtype: `IMAGE`
- **`image2`**
    - The second image to be blended. This image is combined with the first image according to the specified blend mode and factor.
    - Python dtype: `torch.Tensor`
    - Comfy dtype: `IMAGE`
- **`blend_factor`**
    - Determines the weight of each image in the final blend. A higher blend factor gives more weight to the second image.
    - Python dtype: `float`
    - Comfy dtype: `FLOAT`
- **`blend_mode`**
    - Specifies the method of blending the two images. Supports various modes like normal, multiply, screen, overlay, soft light, and difference.
    - Python dtype: `str`
    - Comfy dtype: `['normal', 'multiply', 'screen', 'overlay', 'soft_light', 'difference']`
## Output types
- **`image`**
    - The result of blending the two images together based on the specified blend mode and factor.
    - Python dtype: `torch.Tensor`
    - Comfy dtype: `IMAGE`
## Usage tips
- Infra type: `GPU`
- Common nodes: `ImageCombine,Image Aspect Ratio,ImageSender,Mute / Bypass Repeater (rgthree)`


## Source code
```python
class Blend:
    def __init__(self):
        pass

    @classmethod
    def INPUT_TYPES(s):
        return {
            "required": {
                "image1": ("IMAGE",),
                "image2": ("IMAGE",),
                "blend_factor": ("FLOAT", {
                    "default": 0.5,
                    "min": 0.0,
                    "max": 1.0,
                    "step": 0.01
                }),
                "blend_mode": (["normal", "multiply", "screen", "overlay", "soft_light", "difference"],),
            },
        }

    RETURN_TYPES = ("IMAGE",)
    FUNCTION = "blend_images"

    CATEGORY = "image/postprocessing"

    def blend_images(self, image1: torch.Tensor, image2: torch.Tensor, blend_factor: float, blend_mode: str):
        image2 = image2.to(image1.device)
        if image1.shape != image2.shape:
            image2 = image2.permute(0, 3, 1, 2)
            image2 = comfy.utils.common_upscale(image2, image1.shape[2], image1.shape[1], upscale_method='bicubic', crop='center')
            image2 = image2.permute(0, 2, 3, 1)

        blended_image = self.blend_mode(image1, image2, blend_mode)
        blended_image = image1 * (1 - blend_factor) + blended_image * blend_factor
        blended_image = torch.clamp(blended_image, 0, 1)
        return (blended_image,)

    def blend_mode(self, img1, img2, mode):
        if mode == "normal":
            return img2
        elif mode == "multiply":
            return img1 * img2
        elif mode == "screen":
            return 1 - (1 - img1) * (1 - img2)
        elif mode == "overlay":
            return torch.where(img1 <= 0.5, 2 * img1 * img2, 1 - 2 * (1 - img1) * (1 - img2))
        elif mode == "soft_light":
            return torch.where(img2 <= 0.5, img1 - (1 - 2 * img2) * img1 * (1 - img1), img1 + (2 * img2 - 1) * (self.g(img1) - img1))
        elif mode == "difference":
            return img1 - img2
        else:
            raise ValueError(f"Unsupported blend mode: {mode}")

    def g(self, x):
        return torch.where(x <= 0.25, ((16 * x - 12) * x + 4) * x, torch.sqrt(x))

```
