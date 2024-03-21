# ImageScaleToTotalPixels
## Documentation
- Class name: `ImageScaleToTotalPixels`
- Category: `image/upscaling`
- Output node: `False`

This node scales an image to a specified total number of pixels (megapixels) while maintaining the aspect ratio. It uses various upscale methods to achieve the desired size.
## Input types
### Required
- **`image`**
    - The input image to be scaled. The choice of upscale method and the target megapixels directly influence the quality and dimensions of the output image.
    - Python dtype: `torch.Tensor`
    - Comfy dtype: `IMAGE`
- **`upscale_method`**
    - The method used for upscaling the image. Different methods can affect the quality and characteristics of the upscaled image.
    - Python dtype: `str`
    - Comfy dtype: `STRING`
- **`megapixels`**
    - The target size of the output image in megapixels. This determines the total number of pixels in the scaled image.
    - Python dtype: `float`
    - Comfy dtype: `FLOAT`
## Output types
- **`image`**
    - The scaled image with the specified total number of pixels.
    - Python dtype: `torch.Tensor`
    - Comfy dtype: `IMAGE`
## Usage tips
- Infra type: `GPU`
- Common nodes: `GetImageSize,ImageBatch,DWPreprocessor,VAEEncodeForInpaint,PreviewImage,ImpactImageInfo,Reroute,ControlNetApply,VAEEncode`


## Source code
```python
class ImageScaleToTotalPixels:
    upscale_methods = ["nearest-exact", "bilinear", "area", "bicubic", "lanczos"]
    crop_methods = ["disabled", "center"]

    @classmethod
    def INPUT_TYPES(s):
        return {"required": { "image": ("IMAGE",), "upscale_method": (s.upscale_methods,),
                              "megapixels": ("FLOAT", {"default": 1.0, "min": 0.01, "max": 16.0, "step": 0.01}),
                            }}
    RETURN_TYPES = ("IMAGE",)
    FUNCTION = "upscale"

    CATEGORY = "image/upscaling"

    def upscale(self, image, upscale_method, megapixels):
        samples = image.movedim(-1,1)
        total = int(megapixels * 1024 * 1024)

        scale_by = math.sqrt(total / (samples.shape[3] * samples.shape[2]))
        width = round(samples.shape[3] * scale_by)
        height = round(samples.shape[2] * scale_by)

        s = comfy.utils.common_upscale(samples, width, height, upscale_method, "disabled")
        s = s.movedim(1,-1)
        return (s,)

```
