# UnsamplerDetailerHookProvider
## Documentation
- Class name: `UnsamplerDetailerHookProvider`
- Category: `ImpactPack/Detailer`
- Output node: `False`

UnsamplerDetailerHookProvider is a class designed to provide hooks for detailer operations, specifically focusing on unsampling tasks within the Impact Pack. It leverages the UnsamplerDetailerHook to apply unsampling techniques, enhancing the detail and quality of images or samples during the processing pipeline.
## Input types
### Required
- **`model`**
    - unknown
    - Python dtype: `unknown`
    - Comfy dtype: `MODEL`
- **`steps`**
    - unknown
    - Python dtype: `unknown`
    - Comfy dtype: `INT`
- **`start_end_at_step`**
    - unknown
    - Python dtype: `unknown`
    - Comfy dtype: `INT`
- **`end_end_at_step`**
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
- **`normalize`**
    - unknown
    - Python dtype: `unknown`
    - Comfy dtype: `['disable', 'enable'...]`
- **`positive`**
    - unknown
    - Python dtype: `unknown`
    - Comfy dtype: `CONDITIONING`
- **`negative`**
    - unknown
    - Python dtype: `unknown`
    - Comfy dtype: `CONDITIONING`
- **`schedule_for_cycle`**
    - unknown
    - Python dtype: `unknown`
    - Comfy dtype: `['skip_start', 'from_start'...]`
## Output types
- **`detailer_hook`**
    - The output is a detailer hook specifically tailored for unsampling operations. It is designed to enhance image or sample quality by applying unsampling techniques.
    - Python dtype: `DetailerHook`
    - Comfy dtype: `DETAILER_HOOK`
## Usage tips
- Infra type: `CPU`
- Common nodes: unknown


## Source code
```python
class UnsamplerDetailerHookProvider:
    schedules = ["skip_start", "from_start"]

    @classmethod
    def INPUT_TYPES(s):
        return {"required":
                    {"model": ("MODEL",),
                     "steps": ("INT", {"default": 25, "min": 1, "max": 10000}),
                     "start_end_at_step": ("INT", {"default": 21, "min": 0, "max": 10000}),
                     "end_end_at_step": ("INT", {"default": 24, "min": 0, "max": 10000}),
                     "cfg": ("FLOAT", {"default": 1.0, "min": 0.0, "max": 100.0}),
                     "sampler_name": (comfy.samplers.KSampler.SAMPLERS, ),
                     "scheduler": (comfy.samplers.KSampler.SCHEDULERS, ),
                     "normalize": (["disable", "enable"], ),
                     "positive": ("CONDITIONING", ),
                     "negative": ("CONDITIONING", ),
                     "schedule_for_cycle": (s.schedules,),
                     }}

    RETURN_TYPES = ("DETAILER_HOOK",)
    FUNCTION = "doit"

    CATEGORY = "ImpactPack/Detailer"

    def doit(self, model, steps, start_end_at_step, end_end_at_step, cfg, sampler_name,
             scheduler, normalize, positive, negative, schedule_for_cycle):
        try:
            hook = hooks.UnsamplerDetailerHook(model, steps, start_end_at_step, end_end_at_step, cfg, sampler_name,
                                               scheduler, normalize, positive, negative,
                                               from_start=('from_start' in schedule_for_cycle))

            return (hook, )
        except Exception as e:
            print("[ERROR] UnsamplerDetailerHookProvider: 'ComfyUI Noise' custom node isn't installed. You must install 'BlenderNeko/ComfyUI Noise' extension to use this node.")
            print(f"\t{e}")
            pass

```
