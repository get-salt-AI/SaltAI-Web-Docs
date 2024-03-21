# DenoiseScheduleHookProvider
## Documentation
- Class name: `DenoiseScheduleHookProvider`
- Category: `ImpactPack/Upscale`
- Output node: `False`

This node provides a mechanism to apply a denoising schedule to an image generation process. It selects a specific denoising schedule based on the iteration and applies it to progressively reduce noise in the generated image.
## Input types
### Required
- **`schedule_for_iteration`**
    - Specifies the denoising schedule to be applied during the image generation process. It determines how denoising parameters are adjusted over iterations.
    - Python dtype: `Tuple[str]`
    - Comfy dtype: `STRING`
- **`target_denoise`**
    - Defines the target denoise level to achieve by the end of the generation process. It influences the final clarity and detail of the generated image.
    - Python dtype: `float`
    - Comfy dtype: `FLOAT`
## Output types
- **`pk_hook`**
    - Returns a hook that implements the specified denoising schedule. This hook is used to adjust denoising parameters dynamically during the image generation process.
    - Python dtype: `PixelKSampleHook`
    - Comfy dtype: `PK_HOOK`
## Usage tips
- Infra type: `CPU`
- Common nodes: `PixelKSampleUpscalerProvider`


## Source code
```python
class DenoiseScheduleHookProvider:
    schedules = ["simple"]

    @classmethod
    def INPUT_TYPES(s):
        return {"required": {
                     "schedule_for_iteration": (s.schedules,),
                     "target_denoise": ("FLOAT", {"default": 0.2, "min": 0.0, "max": 1.0, "step": 0.01}),
                    },
                }

    RETURN_TYPES = ("PK_HOOK",)
    FUNCTION = "doit"

    CATEGORY = "ImpactPack/Upscale"

    def doit(self, schedule_for_iteration, target_denoise):
        hook = None
        if schedule_for_iteration == "simple":
            hook = hooks.SimpleDenoiseScheduleHook(target_denoise)

        return (hook, )

```
