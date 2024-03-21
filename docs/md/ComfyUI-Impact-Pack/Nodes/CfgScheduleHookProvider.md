# CfgScheduleHookProvider
## Documentation
- Class name: `CfgScheduleHookProvider`
- Category: `ImpactPack/Upscale`
- Output node: `False`

This node provides a configuration schedule hook for iterations, allowing the adjustment of configuration parameters over time based on a predefined schedule. It's particularly useful for dynamically adjusting parameters during the iterative process of model training or generation to achieve specific outcomes.
## Input types
### Required
- **`schedule_for_iteration`**
    - Specifies the schedule to be used for iteration. The choice of schedule affects how the target configuration parameter is adjusted over time.
    - Python dtype: `Tuple[str]`
    - Comfy dtype: `STRING`
- **`target_cfg`**
    - The target configuration value to be reached by the end of the iteration schedule. This parameter influences the adjustment of configuration settings over the course of iterations.
    - Python dtype: `float`
    - Comfy dtype: `FLOAT`
## Output types
- **`pk_hook`**
    - Returns a hook that applies the specified configuration schedule to the iterative process.
    - Python dtype: `Tuple[SimpleCfgScheduleHook]`
    - Comfy dtype: `PK_HOOK`
## Usage tips
- Infra type: `CPU`
- Common nodes: unknown


## Source code
```python
class CfgScheduleHookProvider:
    schedules = ["simple"]

    @classmethod
    def INPUT_TYPES(s):
        return {"required": {
                     "schedule_for_iteration": (s.schedules,),
                     "target_cfg": ("FLOAT", {"default": 3.0, "min": 0.0, "max": 100.0}),
                    },
                }

    RETURN_TYPES = ("PK_HOOK",)
    FUNCTION = "doit"

    CATEGORY = "ImpactPack/Upscale"

    def doit(self, schedule_for_iteration, target_cfg):
        hook = None
        if schedule_for_iteration == "simple":
            hook = hooks.SimpleCfgScheduleHook(target_cfg)

        return (hook, )

```
