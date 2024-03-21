# Upscale Latent
## Documentation
- Class name: `LatentUpscale`
- Category: `latent`
- Output node: `False`

The LatentUpscale node is designed to upscale latent representations of images. It allows for the specification of the desired output dimensions (width and height) and the method of upscaling. Additionally, it supports cropping of the upscaled image to a specified method. This node is particularly useful for adjusting the resolution of latent images in preparation for further processing or visualization.
## Input types
### Required
- **`samples`**
    - The latent representation of an image to be upscaled. This input is crucial as it provides the base data that the upscaling process will modify.
    - Python dtype: `Dict[str, torch.Tensor]`
    - Comfy dtype: `LATENT`
- **`upscale_method`**
    - Specifies the method used for upscaling the latent image. Different methods can affect the quality and characteristics of the upscaled image.
    - Python dtype: `str`
    - Comfy dtype: `STRING`
- **`width`**
    - The desired width of the upscaled image. This parameter allows for precise control over the dimensions of the output image.
    - Python dtype: `int`
    - Comfy dtype: `INT`
- **`height`**
    - The desired height of the upscaled image. Similar to the width parameter, this allows for setting the specific height of the output.
    - Python dtype: `int`
    - Comfy dtype: `INT`
- **`crop`**
    - Determines how the upscaled image should be cropped. This can be used to focus on or exclude certain parts of the image after upscaling.
    - Python dtype: `str`
    - Comfy dtype: `STRING`
## Output types
- **`latent`**
    - The upscaled latent representation of the image. This output is the result of applying the specified upscaling method and dimensions to the input latent image.
    - Python dtype: `Dict[str, torch.Tensor]`
    - Comfy dtype: `LATENT`
## Usage tips
- Infra type: `GPU`
- Common nodes: `KSampler`


## Source code
```python
class LatentUpscale:
    upscale_methods = ["nearest-exact", "bilinear", "area", "bicubic", "bislerp"]
    crop_methods = ["disabled", "center"]

    @classmethod
    def INPUT_TYPES(s):
        return {"required": { "samples": ("LATENT",), "upscale_method": (s.upscale_methods,),
                              "width": ("INT", {"default": 512, "min": 0, "max": MAX_RESOLUTION, "step": 8}),
                              "height": ("INT", {"default": 512, "min": 0, "max": MAX_RESOLUTION, "step": 8}),
                              "crop": (s.crop_methods,)}}
    RETURN_TYPES = ("LATENT",)
    FUNCTION = "upscale"

    CATEGORY = "latent"

    def upscale(self, samples, upscale_method, width, height, crop):
        if width == 0 and height == 0:
            s = samples
        else:
            s = samples.copy()

            if width == 0:
                height = max(64, height)
                width = max(64, round(samples["samples"].shape[3] * height / samples["samples"].shape[2]))
            elif height == 0:
                width = max(64, width)
                height = max(64, round(samples["samples"].shape[2] * width / samples["samples"].shape[3]))
            else:
                width = max(64, width)
                height = max(64, height)

            s["samples"] = comfy.utils.common_upscale(samples["samples"], width // 8, height // 8, upscale_method, crop)
        return (s,)

```
