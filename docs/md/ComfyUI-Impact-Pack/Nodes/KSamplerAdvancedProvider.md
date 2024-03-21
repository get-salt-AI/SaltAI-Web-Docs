# KSamplerAdvancedProvider
## Documentation
- Class name: `KSamplerAdvancedProvider`
- Category: `ImpactPack/Sampler`
- Output node: `False`

KSamplerAdvancedProvider is a class method that configures and returns an advanced KSampler sampler. It utilizes a basic pipeline and specific configurations such as sampler name and scheduler to create a customized sampling process.
## Input types
### Required
- **`cfg`**
    - Defines the configuration setting for the sampler, influencing its behavior and performance.
    - Python dtype: `float`
    - Comfy dtype: `FLOAT`
- **`sampler_name`**
    - Specifies the name of the sampler to be used, determining the sampling strategy.
    - Python dtype: `str`
    - Comfy dtype: `STRING`
- **`scheduler`**
    - Determines the scheduling strategy for the sampling process, affecting the progression of sampling steps.
    - Python dtype: `str`
    - Comfy dtype: `STRING`
- **`basic_pipe`**
    - Represents the basic pipeline inputs including the model and conditioning information, which are essential for the sampling process.
    - Python dtype: `Tuple[torch.nn.Module, Any, Any, Any, Any]`
    - Comfy dtype: `BASIC_PIPE`
## Output types
- **`ksampler_advanced`**
    - The advanced KSampler sampler configured according to the provided inputs.
    - Python dtype: `KSamplerAdvancedWrapper`
    - Comfy dtype: `KSAMPLER_ADVANCED`
## Usage tips
- Infra type: `GPU`
- Common nodes: unknown


## Source code
```python
class KSamplerAdvancedProvider:
    @classmethod
    def INPUT_TYPES(s):
        return {"required": {
                                "cfg": ("FLOAT", {"default": 8.0, "min": 0.0, "max": 100.0}),
                                "sampler_name": (comfy.samplers.KSampler.SAMPLERS, ),
                                "scheduler": (comfy.samplers.KSampler.SCHEDULERS, ),
                                "basic_pipe": ("BASIC_PIPE", )
                             },
                }

    RETURN_TYPES = ("KSAMPLER_ADVANCED",)
    FUNCTION = "doit"

    CATEGORY = "ImpactPack/Sampler"

    def doit(self, cfg, sampler_name, scheduler, basic_pipe):
        model, _, _, positive, negative = basic_pipe
        sampler = KSamplerAdvancedWrapper(model, cfg, sampler_name, scheduler, positive, negative)
        return (sampler, )

```
