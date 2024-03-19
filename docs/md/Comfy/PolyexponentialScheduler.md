# PolyexponentialScheduler
## Documentation
- Class name: `PolyexponentialScheduler`
- Category: `sampling/custom_sampling/schedulers`
- Output node: `False`

This node generates a sequence of noise levels (sigmas) based on a polyexponential schedule for diffusion processes. It allows for the customization of the noise schedule by adjusting parameters such as the number of steps and the minimum and maximum sigma values, as well as the rho parameter which controls the shape of the noise schedule.
## Input types
### Required
- **`steps`**
    - Specifies the number of steps in the diffusion process. It determines the length of the generated sequence of noise levels.
    - Python dtype: `int`
    - Comfy dtype: `INT`
- **`sigma_max`**
    - The maximum sigma value in the noise schedule. It defines the upper bound of the noise levels.
    - Python dtype: `float`
    - Comfy dtype: `FLOAT`
- **`sigma_min`**
    - The minimum sigma value in the noise schedule. It sets the lower limit of the noise levels.
    - Python dtype: `float`
    - Comfy dtype: `FLOAT`
- **`rho`**
    - Controls the shape of the polyexponential noise schedule, affecting how the noise levels progress between the minimum and maximum sigma values.
    - Python dtype: `float`
    - Comfy dtype: `FLOAT`
## Output types
- **`sigmas`**
    - The generated sequence of noise levels based on the polyexponential schedule.
    - Python dtype: `torch.Tensor`
    - Comfy dtype: `SIGMAS`
## Usage tips
- Infra type: `GPU`
- Common nodes: unknown


## Source code
```python
class PolyexponentialScheduler:
    @classmethod
    def INPUT_TYPES(s):
        return {"required":
                    {"steps": ("INT", {"default": 20, "min": 1, "max": 10000}),
                     "sigma_max": ("FLOAT", {"default": 14.614642, "min": 0.0, "max": 1000.0, "step":0.01, "round": False}),
                     "sigma_min": ("FLOAT", {"default": 0.0291675, "min": 0.0, "max": 1000.0, "step":0.01, "round": False}),
                     "rho": ("FLOAT", {"default": 1.0, "min": 0.0, "max": 100.0, "step":0.01, "round": False}),
                    }
               }
    RETURN_TYPES = ("SIGMAS",)
    CATEGORY = "sampling/custom_sampling/schedulers"

    FUNCTION = "get_sigmas"

    def get_sigmas(self, steps, sigma_max, sigma_min, rho):
        sigmas = k_diffusion_sampling.get_sigmas_polyexponential(n=steps, sigma_min=sigma_min, sigma_max=sigma_max, rho=rho)
        return (sigmas, )

```
