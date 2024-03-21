# PixelTiledKSampleUpscalerProvider
## Documentation
- Class name: `PixelTiledKSampleUpscalerProvider`
- Category: `ImpactPack/Upscale`
- Output node: `False`

The `PixelTiledKSampleUpscalerProvider` node is designed to upscale images using a tiled K-sample upscaling method. It checks for the presence of a specific custom node ('BNK_TiledKSampler') and, if not found, attempts to install it. This node facilitates the use of advanced sampling techniques for image upscaling, leveraging tile-based processing to enhance image quality.
## Input types
### Required
- **`scale_method`**
    - unknown
    - Python dtype: `unknown`
    - Comfy dtype: `['nearest-exact', 'bilinear', 'lanczos'...]`
- **`model`**
    - unknown
    - Python dtype: `unknown`
    - Comfy dtype: `MODEL`
- **`vae`**
    - unknown
    - Python dtype: `unknown`
    - Comfy dtype: `VAE`
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
- **`tile_width`**
    - unknown
    - Python dtype: `unknown`
    - Comfy dtype: `INT`
- **`tile_height`**
    - unknown
    - Python dtype: `unknown`
    - Comfy dtype: `INT`
- **`tiling_strategy`**
    - unknown
    - Python dtype: `unknown`
    - Comfy dtype: `['random', 'padded', 'simple'...]`
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
    - unknown
    - Python dtype: `unknown`
    - Comfy dtype: `UPSCALER`
## Usage tips
- Infra type: `GPU`
- Common nodes: unknown


## Source code
```python
class PixelTiledKSampleUpscalerProvider:
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
                    "tile_width": ("INT", {"default": 512, "min": 320, "max": MAX_RESOLUTION, "step": 64}),
                    "tile_height": ("INT", {"default": 512, "min": 320, "max": MAX_RESOLUTION, "step": 64}),
                    "tiling_strategy": (["random", "padded", 'simple'], ),
                    },
                "optional": {
                        "upscale_model_opt": ("UPSCALE_MODEL", ),
                        "pk_hook_opt": ("PK_HOOK", ),
                    }
                }

    RETURN_TYPES = ("UPSCALER",)
    FUNCTION = "doit"

    CATEGORY = "ImpactPack/Upscale"

    def doit(self, scale_method, model, vae, seed, steps, cfg, sampler_name, scheduler, positive, negative, denoise, tile_width, tile_height, tiling_strategy, upscale_model_opt=None, pk_hook_opt=None):
        if "BNK_TiledKSampler" in nodes.NODE_CLASS_MAPPINGS:
            upscaler = core.PixelTiledKSampleUpscaler(scale_method, model, vae, seed, steps, cfg, sampler_name, scheduler, positive, negative, denoise, tile_width, tile_height, tiling_strategy, upscale_model_opt, pk_hook_opt, tile_size=max(tile_width, tile_height))
            return (upscaler, )
        else:
            print("[ERROR] PixelTiledKSampleUpscalerProvider: ComfyUI_TiledKSampler custom node isn't installed. You must install BlenderNeko/ComfyUI_TiledKSampler extension to use this node.")

```
