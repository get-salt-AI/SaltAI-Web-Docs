# PixelKSampleUpscalerProviderPipe
## Documentation
- Class name: `PixelKSampleUpscalerProviderPipe`
- Category: `ImpactPack/Upscale`
- Output node: `False`

This node acts as a provider for the PixelKSampleUpscaler, facilitating the upscaling process by configuring and initializing the upscaling model based on the provided parameters. It dynamically decides whether to use a tiled or non-tiled upscaling approach based on the availability of a specific node class mapping, ensuring flexibility and adaptability in the upscaling process.
## Input types
### Required
- **`scale_method`**
    - Specifies the method used for scaling, impacting the quality and characteristics of the upscaled image.
    - Python dtype: `str`
    - Comfy dtype: `STRING`
- **`seed`**
    - Determines the random seed used in the upscaling process, affecting the randomness and variation in the output.
    - Python dtype: `int`
    - Comfy dtype: `INT`
- **`steps`**
    - Defines the number of steps in the upscaling process, influencing the detail and refinement of the upscaled image.
    - Python dtype: `int`
    - Comfy dtype: `INT`
- **`cfg`**
    - Configures the upscaling process, allowing for customization of the upscaling behavior.
    - Python dtype: `float`
    - Comfy dtype: `FLOAT`
- **`sampler_name`**
    - Selects the sampler used in the upscaling process, affecting the sampling strategy and output quality.
    - Python dtype: `str`
    - Comfy dtype: `STRING`
- **`scheduler`**
    - Chooses the scheduler for the upscaling process, managing the progression and timing of upscaling steps.
    - Python dtype: `str`
    - Comfy dtype: `STRING`
- **`denoise`**
    - Determines the level of denoising applied during the upscaling process, impacting the clarity and smoothness of the upscaled image.
    - Python dtype: `float`
    - Comfy dtype: `FLOAT`
- **`use_tiled_vae`**
    - Indicates whether a tiled VAE approach is used, affecting the upscaling process's efficiency and output.
    - Python dtype: `bool`
    - Comfy dtype: `BOOLEAN`
- **`basic_pipe`**
    - Provides the basic components required for the upscaling process, including models and configurations.
    - Python dtype: `Tuple[torch.nn.Module, Any, torch.nn.Module, bool, bool]`
    - Comfy dtype: `BASIC_PIPE`
- **`tile_size`**
    - Specifies the size of tiles used in the tiled upscaling approach, affecting the granularity and parallelism of the process.
    - Python dtype: `int`
    - Comfy dtype: `INT`
### Optional
- **`upscale_model_opt`**
    - Optional parameter for specifying additional options for the upscale model, allowing for further customization.
    - Python dtype: `Optional[Dict[str, Any]]`
    - Comfy dtype: `UPSCALE_MODEL`
- **`pk_hook_opt`**
    - Optional parameter for specifying options for post-processing hooks, enabling additional image processing steps after upscaling.
    - Python dtype: `Optional[Dict[str, Any]]`
    - Comfy dtype: `PK_HOOK`
## Output types
- **`upscaler`**
    - The configured upscaling model ready for use, either tiled or non-tiled based on the input parameters and available node class mappings.
    - Python dtype: `PixelTiledKSampleUpscaler`
    - Comfy dtype: `UPSCALER`
## Usage tips
- Infra type: `GPU`
- Common nodes: `IterativeImageUpscale`


## Source code
```python
class PixelKSampleUpscalerProviderPipe(PixelKSampleUpscalerProvider):
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
                    "use_tiled_vae": ("BOOLEAN", {"default": False, "label_on": "enabled", "label_off": "disabled"}),
                    "basic_pipe": ("BASIC_PIPE",),
                    "tile_size": ("INT", {"default": 512, "min": 320, "max": 4096, "step": 64}),
                    },
                "optional": {
                        "upscale_model_opt": ("UPSCALE_MODEL", ),
                        "pk_hook_opt": ("PK_HOOK", ),
                    }
                }

    RETURN_TYPES = ("UPSCALER",)
    FUNCTION = "doit_pipe"

    CATEGORY = "ImpactPack/Upscale"

    def doit_pipe(self, scale_method, seed, steps, cfg, sampler_name, scheduler, denoise,
                  use_tiled_vae, basic_pipe, upscale_model_opt=None, pk_hook_opt=None, tile_size=512):
        model, _, vae, positive, negative = basic_pipe
        upscaler = core.PixelKSampleUpscaler(scale_method, model, vae, seed, steps, cfg, sampler_name, scheduler,
                                             positive, negative, denoise, use_tiled_vae, upscale_model_opt, pk_hook_opt,
                                             tile_size=tile_size)
        return (upscaler, )

```
