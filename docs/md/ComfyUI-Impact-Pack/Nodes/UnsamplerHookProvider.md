# UnsamplerHookProvider
## Documentation
- Class name: `UnsamplerHookProvider`
- Category: `ImpactPack/Upscale`
- Output node: `False`

The UnsamplerHookProvider is designed to create an UnsamplerHook, which is a custom hook for modifying the sampling process. It adjusts the sampling steps dynamically based on the current step in relation to the total number of steps, allowing for a more nuanced control over the sampling process. This is particularly useful for applications requiring precise manipulation of the sampling behavior over time.
## Input types
### Required
- **`model`**
    - The model parameter represents the neural network model that will be used for the sampling process. It is crucial as it defines the architecture and parameters that will be utilized during sampling.
    - Python dtype: `torch.nn.Module`
    - Comfy dtype: `MODEL`
- **`steps`**
    - Specifies the total number of steps in the sampling process. It is important for determining the progression and completion of the sampling.
    - Python dtype: `int`
    - Comfy dtype: `INT`
- **`start_end_at_step`**
    - Indicates the starting step from which the end step adjustment begins. It plays a key role in determining when the dynamic adjustment of the sampling steps should start.
    - Python dtype: `int`
    - Comfy dtype: `INT`
- **`end_end_at_step`**
    - Specifies the final step at which the dynamic adjustment of the sampling steps ends. This parameter is essential for defining the range of the sampling process adjustment.
    - Python dtype: `int`
    - Comfy dtype: `INT`
- **`cfg`**
    - Configuration parameter that influences the behavior of the sampling process. It is a critical setting that can affect the outcome of the sampling.
    - Python dtype: `float`
    - Comfy dtype: `FLOAT`
- **`sampler_name`**
    - The name of the sampler to be used. This parameter determines which sampling algorithm will be applied during the process.
    - Python dtype: `str`
    - Comfy dtype: `STRING`
- **`scheduler`**
    - Defines the scheduling algorithm for the sampling process. It is important for controlling how the sampling steps are adjusted over time.
    - Python dtype: `str`
    - Comfy dtype: `STRING`
- **`normalize`**
    - A boolean parameter that indicates whether the samples should be normalized. This affects the preprocessing of the data before sampling.
    - Python dtype: `bool`
    - Comfy dtype: `BOOLEAN`
- **`positive`**
    - Specifies the positive conditioning for the sampling process. It is used to guide the sampling towards desired outcomes.
    - Python dtype: `str`
    - Comfy dtype: `CONDITIONING`
- **`negative`**
    - Specifies the negative conditioning for the sampling process. It is used to steer the sampling away from undesired outcomes.
    - Python dtype: `str`
    - Comfy dtype: `CONDITIONING`
- **`schedule_for_iteration`**
    - unknown
    - Python dtype: `unknown`
    - Comfy dtype: `['simple'...]`
## Output types
- **`pk_hook`**
    - Returns an instance of UnsamplerHook, which is used to modify the sampling process according to the specified parameters.
    - Python dtype: `UnsamplerHook`
    - Comfy dtype: `PK_HOOK`
## Usage tips
- Infra type: `CPU`
- Common nodes: unknown


## Source code
```python
class UnsamplerHookProvider:
    schedules = ["simple"]

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
                     "schedule_for_iteration": (s.schedules,),
                     }}

    RETURN_TYPES = ("PK_HOOK",)
    FUNCTION = "doit"

    CATEGORY = "ImpactPack/Upscale"

    def doit(self, model, steps, start_end_at_step, end_end_at_step, cfg, sampler_name,
             scheduler, normalize, positive, negative, schedule_for_iteration):
        try:
            hook = None
            if schedule_for_iteration == "simple":
                hook = hooks.UnsamplerHook(model, steps, start_end_at_step, end_end_at_step, cfg, sampler_name,
                                           scheduler, normalize, positive, negative)

            return (hook, )
        except Exception as e:
            print("[ERROR] UnsamplerHookProvider: 'ComfyUI Noise' custom node isn't installed. You must install 'BlenderNeko/ComfyUI Noise' extension to use this node.")
            print(f"\t{e}")
            pass

```
