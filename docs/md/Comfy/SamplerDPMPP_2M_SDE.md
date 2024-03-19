# SamplerDPMPP_2M_SDE
## Documentation
- Class name: `SamplerDPMPP_2M_SDE`
- Category: `sampling/custom_sampling/samplers`
- Output node: `False`

This node generates a sampler for the DPM-Solver++(2M) SDE model, selecting between CPU and GPU based on the noise device input. It configures the sampler with specific parameters like solver type, eta, and s_noise.
## Input types
### Required
- **`solver_type`**
    - Determines the numerical solver used for the SDE. Options are 'midpoint' and 'heun', affecting the numerical stability and accuracy of the sampling process.
    - Python dtype: `List[str]`
    - Comfy dtype: `['STRING']`
- **`eta`**
    - Controls the step size in the SDE solver, influencing the trade-off between sampling quality and computational cost.
    - Python dtype: `float`
    - Comfy dtype: `FLOAT`
- **`s_noise`**
    - Adjusts the scale of the noise used in the sampling process, impacting the diversity of the generated samples.
    - Python dtype: `float`
    - Comfy dtype: `FLOAT`
- **`noise_device`**
    - Specifies the computational device (CPU or GPU) for noise generation, directly affecting performance and compatibility.
    - Python dtype: `str`
    - Comfy dtype: `['STRING']`
## Output types
- **`sampler`**
    - The configured sampler for the DPM-Solver++(2M) SDE model, ready for use in sampling tasks.
    - Python dtype: `comfy.samplers.ksampler`
    - Comfy dtype: `SAMPLER`
## Usage tips
- Infra type: `GPU`
- Common nodes: unknown


## Source code
```python
class SamplerDPMPP_2M_SDE:
    @classmethod
    def INPUT_TYPES(s):
        return {"required":
                    {"solver_type": (['midpoint', 'heun'], ),
                     "eta": ("FLOAT", {"default": 1.0, "min": 0.0, "max": 100.0, "step":0.01, "round": False}),
                     "s_noise": ("FLOAT", {"default": 1.0, "min": 0.0, "max": 100.0, "step":0.01, "round": False}),
                     "noise_device": (['gpu', 'cpu'], ),
                      }
               }
    RETURN_TYPES = ("SAMPLER",)
    CATEGORY = "sampling/custom_sampling/samplers"

    FUNCTION = "get_sampler"

    def get_sampler(self, solver_type, eta, s_noise, noise_device):
        if noise_device == 'cpu':
            sampler_name = "dpmpp_2m_sde"
        else:
            sampler_name = "dpmpp_2m_sde_gpu"
        sampler = comfy.samplers.ksampler(sampler_name, {"eta": eta, "s_noise": s_noise, "solver_type": solver_type})
        return (sampler, )

```
