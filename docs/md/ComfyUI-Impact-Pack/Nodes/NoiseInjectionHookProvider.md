# NoiseInjectionHookProvider
## Documentation
- Class name: `NoiseInjectionHookProvider`
- Category: `ImpactPack/Upscale`
- Output node: `False`

This node provides a mechanism to inject noise into a process at specified intervals or conditions, enhancing the variability or realism of the output. It utilizes a schedule to determine when the noise injection should occur and applies the noise based on the provided parameters.
## Input types
### Required
- **`schedule_for_iteration`**
    - Defines the schedule or condition under which noise is injected. The 'simple' schedule indicates a straightforward, possibly uniform application of noise. The choice of schedule affects how and when the noise is applied, potentially altering the process's behavior or output characteristics over time.
    - Python dtype: `str`
    - Comfy dtype: `STRING`
- **`source`**
    - Specifies the computational resource (CPU or GPU) used for the noise injection process. The choice of source can influence the performance and efficiency of the noise injection, with GPU potentially offering faster processing times.
    - Python dtype: `str`
    - Comfy dtype: `STRING`
- **`seed`**
    - A seed value for random number generation, ensuring reproducibility of the noise pattern. This parameter allows for consistent noise patterns across different runs, which is crucial for comparative analysis or debugging.
    - Python dtype: `int`
    - Comfy dtype: `INT`
- **`start_strength`**
    - The initial strength of the noise to be injected, influencing the intensity of the effect. A higher start strength can lead to more pronounced noise effects initially, which can be adjusted dynamically with the end strength parameter.
    - Python dtype: `float`
    - Comfy dtype: `FLOAT`
- **`end_strength`**
    - The final strength of the noise to be injected, allowing for dynamic adjustment over time. This parameter enables the modulation of noise intensity, potentially leading to more nuanced or realistic effects.
    - Python dtype: `float`
    - Comfy dtype: `FLOAT`
## Output types
- **`pk_hook`**
    - The noise injection hook created by this node, ready to be applied to the target process. It encapsulates the logic for injecting noise based on the specified parameters and schedule.
    - Python dtype: `object`
    - Comfy dtype: `PK_HOOK`
## Usage tips
- Infra type: `CPU`
- Common nodes: unknown


## Source code
```python
class NoiseInjectionHookProvider:
    schedules = ["simple"]

    @classmethod
    def INPUT_TYPES(s):
        return {"required": {
                     "schedule_for_iteration": (s.schedules,),
                     "source": (["CPU", "GPU"],),
                     "seed": ("INT", {"default": 0, "min": 0, "max": 0xffffffffffffffff}),
                     "start_strength": ("FLOAT", {"default": 1.0, "min": 0.0, "max": 200.0, "step": 0.01}),
                     "end_strength": ("FLOAT", {"default": 1.0, "min": 0.0, "max": 200.0, "step": 0.01}),
                    },
                }

    RETURN_TYPES = ("PK_HOOK",)
    FUNCTION = "doit"

    CATEGORY = "ImpactPack/Upscale"

    def doit(self, schedule_for_iteration, source, seed, start_strength, end_strength):
        try:
            hook = None
            if schedule_for_iteration == "simple":
                hook = hooks.InjectNoiseHook(source, seed, start_strength, end_strength)

            return (hook, )
        except Exception as e:
            print("[ERROR] NoiseInjectionHookProvider: 'ComfyUI Noise' custom node isn't installed. You must install 'BlenderNeko/ComfyUI Noise' extension to use this node.")
            print(f"\t{e}")
            pass

```
