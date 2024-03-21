# Latent Scale (on Pixel Space)
## Documentation
- Class name: `LatentPixelScale`
- Category: `ImpactPack/Upscale`
- Output node: `False`

The `LatentPixelScale` node is designed for upscaling latent representations of images. It supports various upscaling methods and can optionally utilize a specific upscaling model for enhanced results. The node can work with both tiled and non-tiled VAEs, allowing for flexible upscaling strategies.
## Input types
### Required
- **`samples`**
    - The latent representation of images to be upscaled. This is the core input for the upscaling process.
    - Python dtype: `torch.Tensor`
    - Comfy dtype: `LATENT`
- **`scale_method`**
    - Specifies the method to be used for upscaling. The choice of method can affect the quality and characteristics of the upscaled image.
    - Python dtype: `str`
    - Comfy dtype: `STRING`
- **`scale_factor`**
    - Determines how much the image should be upscaled. A higher factor results in a larger image.
    - Python dtype: `float`
    - Comfy dtype: `FLOAT`
- **`vae`**
    - The variational autoencoder used for decoding and encoding the latent representations. It plays a crucial role in the upscaling process.
    - Python dtype: `torch.nn.Module`
    - Comfy dtype: `VAE`
- **`use_tiled_vae`**
    - Indicates whether a tiled VAE should be used, which can impact the upscaling performance and results.
    - Python dtype: `bool`
    - Comfy dtype: `BOOLEAN`
### Optional
- **`upscale_model_opt`**
    - An optional upscaling model that can be used for enhanced upscaling results.
    - Python dtype: `torch.nn.Module`
    - Comfy dtype: `UPSCALE_MODEL`
## Output types
- **`latent`**
    - The upscaled latent representation of the image.
    - Python dtype: `torch.Tensor`
    - Comfy dtype: `LATENT`
- **`image`**
    - The upscaled image derived from the latent representation.
    - Python dtype: `torch.Tensor`
    - Comfy dtype: `IMAGE`
## Usage tips
- Infra type: `GPU`
- Common nodes: `VAEDecode,KSampler`


## Source code
```python
class LatentPixelScale:
    upscale_methods = ["nearest-exact", "bilinear", "lanczos", "area"]

    @classmethod
    def INPUT_TYPES(s):
        return {"required": {
                     "samples": ("LATENT", ),
                     "scale_method": (s.upscale_methods,),
                     "scale_factor": ("FLOAT", {"default": 1.5, "min": 0.1, "max": 10000, "step": 0.1}),
                     "vae": ("VAE", ),
                     "use_tiled_vae": ("BOOLEAN", {"default": False, "label_on": "enabled", "label_off": "disabled"}),
                    },
                "optional": {
                        "upscale_model_opt": ("UPSCALE_MODEL", ),
                    }
                }

    RETURN_TYPES = ("LATENT", "IMAGE")
    FUNCTION = "doit"

    CATEGORY = "ImpactPack/Upscale"

    def doit(self, samples, scale_method, scale_factor, vae, use_tiled_vae, upscale_model_opt=None):
        if upscale_model_opt is None:
            latimg = core.latent_upscale_on_pixel_space2(samples, scale_method, scale_factor, vae, use_tile=use_tiled_vae)
        else:
            latimg = core.latent_upscale_on_pixel_space_with_model2(samples, scale_method, upscale_model_opt, scale_factor, vae, use_tile=use_tiled_vae)
        return latimg

```
