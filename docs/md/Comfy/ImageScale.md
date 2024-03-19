# Upscale Image
## Documentation
- Class name: `ImageScale`
- Category: `image/upscaling`
- Output node: `False`

The `ImageScale` node is designed for upscaling images to a specified width and height, with the option to crop the upscaled image. It supports various upscaling methods to adjust the image quality and size according to the user's needs.
## Input types
### Required
- **`image`**
    - The input image to be upscaled. This is the primary data that the node operates on.
    - Python dtype: `torch.Tensor`
    - Comfy dtype: `IMAGE`
- **`upscale_method`**
    - Specifies the method used for upscaling the image. Different methods can affect the quality and characteristics of the upscaled image.
    - Python dtype: `str`
    - Comfy dtype: `STRING`
- **`width`**
    - The target width of the upscaled image. If set to 0, the width is automatically calculated to maintain the aspect ratio based on the specified height.
    - Python dtype: `int`
    - Comfy dtype: `INT`
- **`height`**
    - The target height of the upscaled image. If set to 0, the height is automatically calculated to maintain the aspect ratio based on the specified width.
    - Python dtype: `int`
    - Comfy dtype: `INT`
- **`crop`**
    - Determines whether and how the upscaled image should be cropped. This can be used to focus on a specific area of the image or to fit a particular aspect ratio.
    - Python dtype: `str`
    - Comfy dtype: `STRING`
## Output types
- **`image`**
    - The upscaled (and optionally cropped) version of the input image.
    - Python dtype: `torch.Tensor`
    - Comfy dtype: `IMAGE`
## Usage tips
- Infra type: `GPU`
- Common nodes: `VAEEncode,FILM VFI,LineArtPreprocessor,ControlNetApplyAdvanced,PreviewImage,VAEEncodeTiled,StableZero123_Conditioning,OpenposePreprocessor,IPAdapterApply,Image Rembg (Remove Background)`


## Source code
```python
class ImageScale:
    upscale_methods = ["nearest-exact", "bilinear", "area", "bicubic", "lanczos"]
    crop_methods = ["disabled", "center"]

    @classmethod
    def INPUT_TYPES(s):
        return {"required": { "image": ("IMAGE",), "upscale_method": (s.upscale_methods,),
                              "width": ("INT", {"default": 512, "min": 0, "max": MAX_RESOLUTION, "step": 1}),
                              "height": ("INT", {"default": 512, "min": 0, "max": MAX_RESOLUTION, "step": 1}),
                              "crop": (s.crop_methods,)}}
    RETURN_TYPES = ("IMAGE",)
    FUNCTION = "upscale"

    CATEGORY = "image/upscaling"

    def upscale(self, image, upscale_method, width, height, crop):
        if width == 0 and height == 0:
            s = image
        else:
            samples = image.movedim(-1,1)

            if width == 0:
                width = max(1, round(samples.shape[3] * height / samples.shape[2]))
            elif height == 0:
                height = max(1, round(samples.shape[2] * width / samples.shape[3]))

            s = comfy.utils.common_upscale(samples, width, height, upscale_method, crop)
            s = s.movedim(1,-1)
        return (s,)

```
