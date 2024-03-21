# ImageSharpen
## Documentation
- Class name: `ImageSharpen`
- Category: `image/postprocessing`
- Output node: `False`

The `ImageSharpen` node applies a sharpening filter to an image using a Gaussian kernel. It enhances the edges and details within the image by convolving it with a generated kernel based on the specified sharpen radius, sigma, and alpha parameters. The process involves adjusting the kernel to ensure the sum of its elements equals one, applying padding to the image, and then performing a 2D convolution. The result is clamped to the range [0, 1] to maintain valid image values.
## Input types
### Required
- **`image`**
    - The input image to be sharpened. It is a tensor representing the image data.
    - Python dtype: `torch.Tensor`
    - Comfy dtype: `IMAGE`
- **`sharpen_radius`**
    - Specifies the radius of the sharpening effect. A larger radius increases the area of influence for the sharpening, affecting more pixels around the edges.
    - Python dtype: `int`
    - Comfy dtype: `INT`
- **`sigma`**
    - Controls the spread of the Gaussian kernel used for sharpening. A higher sigma value results in a smoother, less localized sharpening effect.
    - Python dtype: `float`
    - Comfy dtype: `FLOAT`
- **`alpha`**
    - Determines the strength of the sharpening effect. Higher alpha values produce a more pronounced sharpening.
    - Python dtype: `float`
    - Comfy dtype: `FLOAT`
## Output types
- **`image`**
    - The sharpened version of the input image, with enhanced edges and details.
    - Python dtype: `torch.Tensor`
    - Comfy dtype: `IMAGE`
## Usage tips
- Infra type: `GPU`
- Common nodes: `PreviewImage,IPAdapterApplyFaceID,IPAdapterApply,ImageUpscaleWithModel,UltimateSDUpscale,VAEEncode`


## Source code
```python
class Sharpen:
    def __init__(self):
        pass

    @classmethod
    def INPUT_TYPES(s):
        return {
            "required": {
                "image": ("IMAGE",),
                "sharpen_radius": ("INT", {
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
                "alpha": ("FLOAT", {
                    "default": 1.0,
                    "min": 0.0,
                    "max": 5.0,
                    "step": 0.1
                }),
            },
        }

    RETURN_TYPES = ("IMAGE",)
    FUNCTION = "sharpen"

    CATEGORY = "image/postprocessing"

    def sharpen(self, image: torch.Tensor, sharpen_radius: int, sigma:float, alpha: float):
        if sharpen_radius == 0:
            return (image,)

        batch_size, height, width, channels = image.shape

        kernel_size = sharpen_radius * 2 + 1
        kernel = gaussian_kernel(kernel_size, sigma, device=image.device) * -(alpha*10)
        center = kernel_size // 2
        kernel[center, center] = kernel[center, center] - kernel.sum() + 1.0
        kernel = kernel.repeat(channels, 1, 1).unsqueeze(1)

        tensor_image = image.permute(0, 3, 1, 2) # Torch wants (B, C, H, W) we use (B, H, W, C)
        tensor_image = F.pad(tensor_image, (sharpen_radius,sharpen_radius,sharpen_radius,sharpen_radius), 'reflect')
        sharpened = F.conv2d(tensor_image, kernel, padding=center, groups=channels)[:,:,sharpen_radius:-sharpen_radius, sharpen_radius:-sharpen_radius]
        sharpened = sharpened.permute(0, 2, 3, 1)

        result = torch.clamp(sharpened, 0, 1)

        return (result,)

```
