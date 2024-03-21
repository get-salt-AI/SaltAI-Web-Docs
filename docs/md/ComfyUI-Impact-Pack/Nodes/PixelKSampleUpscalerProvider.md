# PixelKSampleUpscalerProvider
## Documentation
- Class name: `PixelKSampleUpscalerProvider`
- Category: `ImpactPack/Upscale`
- Output node: `False`

This node acts as a provider for the PixelKSampleUpscaler, facilitating the upscale process of images through a specified upscaling method. It serves as an interface to configure and utilize the PixelKSampleUpscaler, enabling enhanced image resolution and quality.
## Input types
### Required
- **`scale_method`**
    - Specifies the method used for scaling the image, impacting the quality and characteristics of the upscaled image.
    - Python dtype: `str`
    - Comfy dtype: `STRING`
- **`model`**
    - The model used for upscaling, determining the algorithm and parameters for image enhancement.
    - Python dtype: `str`
    - Comfy dtype: `MODEL`
- **`vae`**
    - Variational Autoencoder used in conjunction with the model to improve the upscaling process by generating high-quality details.
    - Python dtype: `str`
    - Comfy dtype: `STRING`
- **`seed`**
    - unknown
    - Python dtype: `unknown`
    - Comfy dtype: `INT`
- **`steps`**
    - unknown
    - Python dtype: `unknown`
    - Comfy dtype: `INT`
- **`cfg`**
    - unknown
    - Python dtype: `unknown`
    - Comfy dtype: `FLOAT`
- **`sampler_name`**
    - unknown
    - Python dtype: `unknown`
    - Comfy dtype: `['euler', 'euler_ancestral', 'heun'...]`
- **`scheduler`**
    - unknown
    - Python dtype: `unknown`
    - Comfy dtype: `['normal', 'karras', 'exponential'...]`
- **`positive`**
    - unknown
    - Python dtype: `unknown`
    - Comfy dtype: `CONDITIONING`
- **`negative`**
    - unknown
    - Python dtype: `unknown`
    - Comfy dtype: `CONDITIONING`
- **`denoise`**
    - unknown
    - Python dtype: `unknown`
    - Comfy dtype: `FLOAT`
- **`use_tiled_vae`**
    - unknown
    - Python dtype: `unknown`
    - Comfy dtype: `BOOLEAN`
- **`tile_size`**
    - unknown
    - Python dtype: `unknown`
    - Comfy dtype: `INT`
### Optional
- **`upscale_model_opt`**
    - unknown
    - Python dtype: `unknown`
    - Comfy dtype: `UPSCALE_MODEL`
- **`pk_hook_opt`**
    - unknown
    - Python dtype: `unknown`
    - Comfy dtype: `PK_HOOK`
## Output types
- **`upscaler`**
    - Provides an instance of PixelKSampleUpscaler configured with the specified parameters, ready for image upscaling tasks.
    - Python dtype: `PixelKSampleUpscaler`
    - Comfy dtype: `UPSCALER`
## Usage tips
- Infra type: `GPU`
- Common nodes: `IterativeLatentUpscale`


## Source code
```python
class PixelKSampleUpscalerProvider:
    upscale_methods = ["nearest-exact", "bilinear", "lanczos", "area"]

    @classmethod
    def INPUT_TYPES(s):
        return {"required": {
                    "scale_method": (s.upscale_methods,),
                    "model": ("MODEL",),
                    "vae": ("VAE",),
                    "seed": ("INT", {"default": 0, "min": 0, "max": 0xffffffffffffffff}),
                    "steps": ("INT", {"default": 20, "min": 1, "max": 10000}),
                    "cfg": ("FLOAT", {"default": 8.0, "min": 0.0, "max": 100.0}),
                    "sampler_name": (comfy.samplers.KSampler.SAMPLERS, ),
                    "scheduler": (comfy.samplers.KSampler.SCHEDULERS, ),
                    "positive": ("CONDITIONING", ),
                    "negative": ("CONDITIONING", ),
                    "denoise": ("FLOAT", {"default": 1.0, "min": 0.0, "max": 1.0, "step": 0.01}),
                    "use_tiled_vae": ("BOOLEAN", {"default": False, "label_on": "enabled", "label_off": "disabled"}),
                    "tile_size": ("INT", {"default": 512, "min": 320, "max": 4096, "step": 64}),
                    },
                "optional": {
                        "upscale_model_opt": ("UPSCALE_MODEL", ),
                        "pk_hook_opt": ("PK_HOOK", ),
                    }
                }

    RETURN_TYPES = ("UPSCALER",)
    FUNCTION = "doit"

    CATEGORY = "ImpactPack/Upscale"

    def doit(self, scale_method, model, vae, seed, steps, cfg, sampler_name, scheduler, positive, negative, denoise,
             use_tiled_vae, upscale_model_opt=None, pk_hook_opt=None, tile_size=512):
        upscaler = core.PixelKSampleUpscaler(scale_method, model, vae, seed, steps, cfg, sampler_name, scheduler,
                                             positive, negative, denoise, use_tiled_vae, upscale_model_opt, pk_hook_opt,
                                             tile_size=tile_size)
        return (upscaler, )

```
