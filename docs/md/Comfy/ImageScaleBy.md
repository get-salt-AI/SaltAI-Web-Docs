# Upscale Image By
## Documentation
- Class name: `ImageScaleBy`
- Category: `image/upscaling`
- Output node: `False`

The `ImageScaleBy` node is designed for upscaling images by a specified scale factor. It adjusts the dimensions of the input image based on the provided scale factor, using one of the specified upscale methods to maintain image quality.
## Input types
### Required
- **`image`**
    - The input image to be upscaled. This parameter is crucial as it directly influences the output image's resolution and appearance.
    - Python dtype: `torch.Tensor`
    - Comfy dtype: `IMAGE`
- **`upscale_method`**
    - Determines the algorithm used for upscaling the image. Different methods can affect the quality and characteristics of the upscaled image.
    - Python dtype: `str`
    - Comfy dtype: `STRING`
- **`scale_by`**
    - The factor by which the image's dimensions will be increased. Affects the final size of the upscaled image.
    - Python dtype: `float`
    - Comfy dtype: `FLOAT`
## Output types
- **`image`**
    - The upscaled image, with dimensions adjusted according to the specified scale factor and upscale method.
    - Python dtype: `torch.Tensor`
    - Comfy dtype: `IMAGE`
## Usage tips
- Infra type: `GPU`
- Common nodes: `VAEEncode,RIFE VFI,VHS_VideoCombine,VAEEncodeTiled,Reroute,PreviewImage,SaveImage,SVD_img2vid_Conditioning,SEGSPreview,ImageFilterSharpen`


## Source code
```python
class ImageScaleBy:
    upscale_methods = ["nearest-exact", "bilinear", "area", "bicubic", "lanczos"]

    @classmethod
    def INPUT_TYPES(s):
        return {"required": { "image": ("IMAGE",), "upscale_method": (s.upscale_methods,),
                              "scale_by": ("FLOAT", {"default": 1.0, "min": 0.01, "max": 8.0, "step": 0.01}),}}
    RETURN_TYPES = ("IMAGE",)
    FUNCTION = "upscale"

    CATEGORY = "image/upscaling"

    def upscale(self, image, upscale_method, scale_by):
        samples = image.movedim(-1,1)
        width = round(samples.shape[3] * scale_by)
        height = round(samples.shape[2] * scale_by)
        s = comfy.utils.common_upscale(samples, width, height, upscale_method, "disabled")
        s = s.movedim(1,-1)
        return (s,)

```
