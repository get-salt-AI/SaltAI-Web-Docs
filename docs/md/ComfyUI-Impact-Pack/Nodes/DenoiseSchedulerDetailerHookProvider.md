# DenoiseSchedulerDetailerHookProvider
## Documentation
- Class name: `DenoiseSchedulerDetailerHookProvider`
- Category: `ImpactPack/Detailer`
- Output node: `False`

This node provides a mechanism to schedule denoising operations in a detailer process, allowing for the dynamic adjustment of denoise levels throughout the cycle based on a predefined schedule.
## Input types
### Required
- **`schedule_for_cycle`**
    - Specifies the scheduling strategy for denoising operations. It determines how denoise levels are adjusted over time.
    - Python dtype: `Tuple[str]`
    - Comfy dtype: `['simple']`
- **`target_denoise`**
    - Defines the target denoise level to achieve by the end of the cycle. It influences the final quality of the detailer output.
    - Python dtype: `float`
    - Comfy dtype: `FLOAT`
## Output types
- **`detailer_hook`**
    - Produces a detailer hook configured to perform denoising operations according to the specified schedule and target denoise level.
    - Python dtype: `SimpleDetailerDenoiseSchedulerHook`
    - Comfy dtype: `DETAILER_HOOK`
## Usage tips
- Infra type: `CPU`
- Common nodes: unknown


## Source code
```python
class DenoiseSchedulerDetailerHookProvider:
    schedules = ["simple"]

    @classmethod
    def INPUT_TYPES(s):
        return {"required": {
                     "schedule_for_cycle": (s.schedules,),
                     "target_denoise": ("FLOAT", {"default": 0.3, "min": 0.0, "max": 1.0, "step": 0.01}),
                    },
                }

    RETURN_TYPES = ("DETAILER_HOOK",)
    FUNCTION = "doit"

    CATEGORY = "ImpactPack/Detailer"

    def doit(self, schedule_for_cycle, target_denoise):
        hook = hooks.SimpleDetailerDenoiseSchedulerHook(target_denoise)
        return (hook, )

```
