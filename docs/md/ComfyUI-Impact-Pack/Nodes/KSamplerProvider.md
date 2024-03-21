# KSamplerProvider
## Documentation
- Class name: `KSamplerProvider`
- Category: `ImpactPack/Sampler`
- Output node: `False`

The KSamplerProvider node provides a mechanism to create a sampler object based on a predefined configuration and a basic pipeline. It initializes a KSamplerWrapper with the provided model, seed, steps, configuration, sampler name, scheduler, denoise level, and the positive and negative conditioning from the basic pipeline. This sampler can then be used for generating samples.
## Input types
### Required
- **`seed`**
    - The seed parameter ensures reproducibility of the sampling process by initializing the random number generator with a specific value.
    - Python dtype: `int`
    - Comfy dtype: `INT`
- **`steps`**
    - The steps parameter determines the number of iterations the sampling process will perform, affecting the quality and the time of generation.
    - Python dtype: `int`
    - Comfy dtype: `INT`
- **`cfg`**
    - The cfg parameter adjusts the configuration of the sampling process, influencing the behavior and characteristics of the generated samples.
    - Python dtype: `float`
    - Comfy dtype: `FLOAT`
- **`sampler_name`**
    - The sampler_name parameter selects the specific sampling algorithm to be used, allowing for customization of the sampling process.
    - Python dtype: `str`
    - Comfy dtype: `STRING`
- **`scheduler`**
    - The scheduler parameter selects the scheduling algorithm for controlling the sampling process, impacting the progression of sample generation.
    - Python dtype: `str`
    - Comfy dtype: `STRING`
- **`denoise`**
    - The denoise parameter controls the level of denoising applied to the generated samples, affecting their clarity and detail.
    - Python dtype: `float`
    - Comfy dtype: `FLOAT`
- **`basic_pipe`**
    - The basic_pipe parameter provides the necessary components for the sampling process, including the model and conditioning information.
    - Python dtype: `Tuple[torch.nn.Module, Any, Any, Any, Any]`
    - Comfy dtype: `BASIC_PIPE`
## Output types
- **`ksampler`**
    - The sampler object configured and initialized for generating samples based on the provided parameters.
    - Python dtype: `KSamplerWrapper`
    - Comfy dtype: `KSAMPLER`
## Usage tips
- Infra type: `GPU`
- Common nodes: unknown


## Source code
```python
class KSamplerProvider:
    @classmethod
    def INPUT_TYPES(s):
        return {"required": {
                                "seed": ("INT", {"default": 0, "min": 0, "max": 0xffffffffffffffff}),
                                "steps": ("INT", {"default": 20, "min": 1, "max": 10000}),
                                "cfg": ("FLOAT", {"default": 8.0, "min": 0.0, "max": 100.0}),
                                "sampler_name": (comfy.samplers.KSampler.SAMPLERS, ),
                                "scheduler": (comfy.samplers.KSampler.SCHEDULERS, ),
                                "denoise": ("FLOAT", {"default": 1.0, "min": 0.0, "max": 1.0, "step": 0.01}),
                                "basic_pipe": ("BASIC_PIPE", )
                             },
                }

    RETURN_TYPES = ("KSAMPLER",)
    FUNCTION = "doit"

    CATEGORY = "ImpactPack/Sampler"

    def doit(self, seed, steps, cfg, sampler_name, scheduler, denoise, basic_pipe):
        model, _, _, positive, negative = basic_pipe
        sampler = KSamplerWrapper(model, seed, steps, cfg, sampler_name, scheduler, positive, negative, denoise)
        return (sampler, )

```
