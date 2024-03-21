# PixelTiledKSampleUpscalerProviderPipe
## Documentation
- Class name: `PixelTiledKSampleUpscalerProviderPipe`
- Category: `ImpactPack/Upscale`
- Output node: `False`

This node is responsible for providing an upscaling service that utilizes a tiled K-sample upscaling method. It checks for the presence of a specific custom node ('BNK_TiledKSampler') and, if available, creates an upscaler instance with the provided parameters. If the required custom node is not installed, it notifies the user about the missing dependency.
## Input types
### Required
- **`scale_method`**
    - Specifies the method used for scaling images, impacting the quality and characteristics of the upscaled output.
    - Python dtype: `str`
    - Comfy dtype: `STRING`
- **`seed`**
    - Determines the random seed used in the upscaling process, affecting the randomness and variation in the upscaled images.
    - Python dtype: `int`
    - Comfy dtype: `INT`
- **`steps`**
    - Defines the number of steps to perform during upscaling, influencing the detail and quality of the result.
    - Python dtype: `int`
    - Comfy dtype: `INT`
- **`cfg`**
    - Configuration settings for the upscaling process, allowing customization of the upscaling behavior.
    - Python dtype: `float`
    - Comfy dtype: `FLOAT`
- **`sampler_name`**
    - The name of the sampler to use, which affects the sampling strategy and the final image quality.
    - Python dtype: `str`
    - Comfy dtype: `STRING`
- **`scheduler`**
    - Specifies the scheduler for controlling the upscaling process, impacting the progression and quality of upscaling.
    - Python dtype: `str`
    - Comfy dtype: `STRING`
- **`denoise`**
    - Indicates whether denoising is applied during upscaling, improving the visual quality of the output by reducing noise.
    - Python dtype: `bool`
    - Comfy dtype: `BOOLEAN`
- **`tile_width`**
    - The width of the tiles used in the tiling strategy, affecting the granularity of the upscaling process.
    - Python dtype: `int`
    - Comfy dtype: `INT`
- **`tile_height`**
    - The height of the tiles used in the tiling strategy, affecting the granularity of the upscaling process.
    - Python dtype: `int`
    - Comfy dtype: `INT`
- **`tiling_strategy`**
    - The strategy used for tiling during upscaling, which influences how the image is divided and processed in parts.
    - Python dtype: `str`
    - Comfy dtype: `STRING`
- **`basic_pipe`**
    - A tuple containing the basic components required for the upscaling process, including models and configurations.
    - Python dtype: `Tuple[torch.nn.Module, Any, torch.nn.Module, bool, bool]`
    - Comfy dtype: `TUPLE`
### Optional
- **`upscale_model_opt`**
    - Optional settings for the upscale model, allowing further customization of the upscaling process.
    - Python dtype: `Optional[Dict[str, Any]]`
    - Comfy dtype: `UPSCALE_MODEL`
- **`pk_hook_opt`**
    - Optional settings for post-processing hooks, enabling additional processing steps after upscaling.
    - Python dtype: `Optional[Dict[str, Any]]`
    - Comfy dtype: `PK_HOOK`
## Output types
- **`upscaler`**
    - The upscaler instance created with the provided parameters, ready to perform the upscaling operation.
    - Python dtype: `PixelTiledKSampleUpscaler`
    - Comfy dtype: `UPSCALER`
## Usage tips
- Infra type: `GPU`
- Common nodes: unknown


## Source code
```python
class PixelTiledKSampleUpscalerProviderPipe:
    upscale_methods = ["nearest-exact", "bilinear", "lanczos", "area"]

    @classmethod
    def INPUT_TYPES(s):
        return {"required": {
                    "scale_method": (s.upscale_methods,),
                    "seed": ("INT", {"default": 0, "min": 0, "max": 0xffffffffffffffff}),
                    "steps": ("INT", {"default": 20, "min": 1, "max": 10000}),
                    "cfg": ("FLOAT", {"default": 8.0, "min": 0.0, "max": 100.0}),
                    "sampler_name": (comfy.samplers.KSampler.SAMPLERS, ),
                    "scheduler": (comfy.samplers.KSampler.SCHEDULERS, ),
                    "denoise": ("FLOAT", {"default": 1.0, "min": 0.0, "max": 1.0, "step": 0.01}),
                    "tile_width": ("INT", {"default": 512, "min": 320, "max": MAX_RESOLUTION, "step": 64}),
                    "tile_height": ("INT", {"default": 512, "min": 320, "max": MAX_RESOLUTION, "step": 64}),
                    "tiling_strategy": (["random", "padded", 'simple'], ),
                    "basic_pipe": ("BASIC_PIPE",)
                    },
                "optional": {
                        "upscale_model_opt": ("UPSCALE_MODEL", ),
                        "pk_hook_opt": ("PK_HOOK", ),
                    }
                }

    RETURN_TYPES = ("UPSCALER",)
    FUNCTION = "doit"

    CATEGORY = "ImpactPack/Upscale"

    def doit(self, scale_method, seed, steps, cfg, sampler_name, scheduler, denoise, tile_width, tile_height, tiling_strategy, basic_pipe, upscale_model_opt=None, pk_hook_opt=None):
        if "BNK_TiledKSampler" in nodes.NODE_CLASS_MAPPINGS:
            model, _, vae, positive, negative = basic_pipe
            upscaler = core.PixelTiledKSampleUpscaler(scale_method, model, vae, seed, steps, cfg, sampler_name, scheduler, positive, negative, denoise, tile_width, tile_height, tiling_strategy, upscale_model_opt, pk_hook_opt, tile_size=max(tile_width, tile_height))
            return (upscaler, )
        else:
            print("[ERROR] PixelTiledKSampleUpscalerProviderPipe: ComfyUI_TiledKSampler custom node isn't installed. You must install BlenderNeko/ComfyUI_TiledKSampler extension to use this node.")

```
