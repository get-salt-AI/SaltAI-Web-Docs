# NoiseInjectionDetailerHookProvider
## Documentation
- Class name: `NoiseInjectionDetailerHookProvider`
- Category: `ImpactPack/Detailer`
- Output node: `False`

This node provides a mechanism to inject noise into the detailer process, allowing for more nuanced control over the noise characteristics throughout the cycle. It utilizes a schedule to determine when and how the noise injection should occur, and it can operate on either CPU or GPU sources.
## Input types
### Required
- **`schedule_for_cycle`**
    - Specifies the schedule according to which noise injection will be applied throughout the cycle. It can either skip the start or begin from the start, affecting the timing and progression of noise injection.
    - Python dtype: `Tuple[str]`
    - Comfy dtype: `STRING`
- **`source`**
    - Determines whether the noise injection process will utilize the CPU or GPU, impacting performance and compatibility.
    - Python dtype: `Tuple[str]`
    - Comfy dtype: `STRING`
- **`seed`**
    - A seed for random number generation, ensuring reproducibility of the noise injection process.
    - Python dtype: `int`
    - Comfy dtype: `INT`
- **`start_strength`**
    - The initial strength of the noise to be injected, which can be adjusted to control the intensity of the effect at the beginning of the cycle.
    - Python dtype: `float`
    - Comfy dtype: `FLOAT`
- **`end_strength`**
    - The final strength of the noise to be injected, allowing for dynamic adjustment of the noise intensity towards the end of the cycle.
    - Python dtype: `float`
    - Comfy dtype: `FLOAT`
## Output types
- **`detailer_hook`**
    - Produces a detailer hook configured for noise injection, which can be used to modify the detailing process by injecting noise according to the specified parameters.
    - Python dtype: `hooks.InjectNoiseHookForDetailer`
    - Comfy dtype: `DETAILER_HOOK`
## Usage tips
- Infra type: `GPU`
- Common nodes: `ToDetailerPipe,FaceDetailer`


## Source code
```python
class NoiseInjectionDetailerHookProvider:
    schedules = ["skip_start", "from_start"]

    @classmethod
    def INPUT_TYPES(s):
        return {"required": {
                     "schedule_for_cycle": (s.schedules,),
                     "source": (["CPU", "GPU"],),
                     "seed": ("INT", {"default": 0, "min": 0, "max": 0xffffffffffffffff}),
                     "start_strength": ("FLOAT", {"default": 2.0, "min": 0.0, "max": 200.0, "step": 0.01}),
                     "end_strength": ("FLOAT", {"default": 1.0, "min": 0.0, "max": 200.0, "step": 0.01}),
                    },
                }

    RETURN_TYPES = ("DETAILER_HOOK",)
    FUNCTION = "doit"

    CATEGORY = "ImpactPack/Detailer"

    def doit(self, schedule_for_cycle, source, seed, start_strength, end_strength):
        try:
            hook = hooks.InjectNoiseHookForDetailer(source, seed, start_strength, end_strength,
                                                    from_start=('from_start' in schedule_for_cycle))
            return (hook, )
        except Exception as e:
            print("[ERROR] NoiseInjectionDetailerHookProvider: 'ComfyUI Noise' custom node isn't installed. You must install 'BlenderNeko/ComfyUI Noise' extension to use this node.")
            print(f"\t{e}")
            pass

```
