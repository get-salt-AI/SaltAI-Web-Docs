# ImageBlur
## Documentation
- Class name: `ImageBlur`
- Category: `image/postprocessing`
- Output node: `False`

The ImageBlur node applies a Gaussian blur to an input image. It uses a Gaussian kernel to smooth the image, which can be adjusted by specifying the blur radius and sigma value. This process can reduce image noise and detail, making it useful for post-processing effects or preparing images for further processing steps.
## Input types
### Required
- **`image`**
    - The input image to be blurred. This is the primary target for the blur effect.
    - Python dtype: `torch.Tensor`
    - Comfy dtype: `IMAGE`
- **`blur_radius`**
    - Determines the size of the Gaussian kernel used for blurring. A larger radius increases the blur effect.
    - Python dtype: `int`
    - Comfy dtype: `INT`
- **`sigma`**
    - Controls the spread of the blur. A higher sigma value results in a smoother, more widespread blur.
    - Python dtype: `float`
    - Comfy dtype: `FLOAT`
## Output types
- **`image`**
    - The blurred version of the input image.
    - Python dtype: `torch.Tensor`
    - Comfy dtype: `IMAGE`
## Usage tips
- Infra type: `GPU`
- Common nodes: `ImageUpscaleWithModel,Cut By Mask`


## Source code
```python
class Blur:
    def __init__(self):
        pass

    @classmethod
    def INPUT_TYPES(s):
        return {
            "required": {
                "image": ("IMAGE",),
                "blur_radius": ("INT", {
                    "default": 1,
                    "min": 1,
                    "max": 31,
                    "step": 1
                }),
                "sigma": ("FLOAT", {
                    "default": 1.0,
                    "min": 0.1,
                    "max": 10.0,
                    "step": 0.1
                }),
            },
        }

    RETURN_TYPES = ("IMAGE",)
    FUNCTION = "blur"

    CATEGORY = "image/postprocessing"

    def blur(self, image: torch.Tensor, blur_radius: int, sigma: float):
        if blur_radius == 0:
            return (image,)

        batch_size, height, width, channels = image.shape

        kernel_size = blur_radius * 2 + 1
        kernel = gaussian_kernel(kernel_size, sigma, device=image.device).repeat(channels, 1, 1).unsqueeze(1)

        image = image.permute(0, 3, 1, 2) # Torch wants (B, C, H, W) we use (B, H, W, C)
        padded_image = F.pad(image, (blur_radius,blur_radius,blur_radius,blur_radius), 'reflect')
        blurred = F.conv2d(padded_image, kernel, padding=kernel_size // 2, groups=channels)[:,:,blur_radius:-blur_radius, blur_radius:-blur_radius]
        blurred = blurred.permute(0, 2, 3, 1)

        return (blurred,)

```
