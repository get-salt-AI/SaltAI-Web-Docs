# SamplerDPMPP_SDE
## Documentation
- Class name: `SamplerDPMPP_SDE`
- Category: `sampling/custom_sampling/samplers`
- Output node: `False`

This node defines a custom sampler for DPM-Solver++ SDE (Stochastic Differential Equations) models, specifically tailored for scenarios where different noise levels and reduction factors are considered. It dynamically selects between CPU and GPU implementations based on the specified noise device, enhancing flexibility and performance optimization.
## Input types
### Required
- **`eta`**
    - Controls the strength of the noise applied during the sampling process, influencing the model's ability to generate diverse outputs.
    - Python dtype: `float`
    - Comfy dtype: `FLOAT`
- **`s_noise`**
    - Determines the scale of noise used in the sampling process, affecting the detail and quality of generated samples.
    - Python dtype: `float`
    - Comfy dtype: `FLOAT`
- **`r`**
    - A factor that adjusts the rate at which noise is reduced throughout the sampling process, impacting the smoothness and coherence of the output.
    - Python dtype: `float`
    - Comfy dtype: `FLOAT`
- **`noise_device`**
    - Specifies the computational device (CPU or GPU) for noise generation, allowing for performance optimization based on available hardware.
    - Python dtype: `str`
    - Comfy dtype: `['gpu', 'cpu']`
## Output types
- **`sampler`**
    - The configured sampler object ready for use in generating samples from the model.
    - Python dtype: `comfy.samplers.ksampler`
    - Comfy dtype: `SAMPLER`
## Usage tips
- Infra type: `GPU`
- Common nodes: unknown


## Source code
```python
class SamplerDPMPP_SDE:
    @classmethod
    def INPUT_TYPES(s):
        return {"required":
                    {"eta": ("FLOAT", {"default": 1.0, "min": 0.0, "max": 100.0, "step":0.01, "round": False}),
                     "s_noise": ("FLOAT", {"default": 1.0, "min": 0.0, "max": 100.0, "step":0.01, "round": False}),
                     "r": ("FLOAT", {"default": 0.5, "min": 0.0, "max": 100.0, "step":0.01, "round": False}),
                     "noise_device": (['gpu', 'cpu'], ),
                      }
               }
    RETURN_TYPES = ("SAMPLER",)
    CATEGORY = "sampling/custom_sampling/samplers"

    FUNCTION = "get_sampler"

    def get_sampler(self, eta, s_noise, r, noise_device):
        if noise_device == 'cpu':
            sampler_name = "dpmpp_sde"
        else:
            sampler_name = "dpmpp_sde_gpu"
        sampler = comfy.samplers.ksampler(sampler_name, {"eta": eta, "s_noise": s_noise, "r": r})
        return (sampler, )

```
