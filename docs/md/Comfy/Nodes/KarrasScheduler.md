# KarrasScheduler
## Documentation
- Class name: `KarrasScheduler`
- Category: `sampling/custom_sampling/schedulers`
- Output node: `False`

The KarrasScheduler node is designed to generate a sequence of noise levels (sigmas) based on the Karras et al. (2022) noise schedule. This sequence is used in diffusion models to gradually add or remove noise from images during the sampling process.
## Input types
### Required
- **`steps`**
    - Specifies the number of steps in the noise schedule. A higher number of steps results in a more gradual transition of noise levels.
    - Python dtype: `int`
    - Comfy dtype: `INT`
- **`sigma_max`**
    - The maximum noise level in the schedule. It defines the starting point of noise addition or the end point of noise removal.
    - Python dtype: `float`
    - Comfy dtype: `FLOAT`
- **`sigma_min`**
    - The minimum noise level in the schedule. It defines the end point of noise addition or the starting point of noise removal.
    - Python dtype: `float`
    - Comfy dtype: `FLOAT`
- **`rho`**
    - A parameter that controls the shape of the noise schedule curve, affecting how quickly or slowly the noise levels change.
    - Python dtype: `float`
    - Comfy dtype: `FLOAT`
## Output types
- **`sigmas`**
    - A tensor of noise levels (sigmas) generated according to the Karras et al. (2022) noise schedule.
    - Python dtype: `torch.Tensor`
    - Comfy dtype: `SIGMAS`
## Usage tips
- Infra type: `GPU`
- Common nodes: unknown


## Source code
```python
class KarrasScheduler:
    @classmethod
    def INPUT_TYPES(s):
        return {"required":
                    {"steps": ("INT", {"default": 20, "min": 1, "max": 10000}),
                     "sigma_max": ("FLOAT", {"default": 14.614642, "min": 0.0, "max": 1000.0, "step":0.01, "round": False}),
                     "sigma_min": ("FLOAT", {"default": 0.0291675, "min": 0.0, "max": 1000.0, "step":0.01, "round": False}),
                     "rho": ("FLOAT", {"default": 7.0, "min": 0.0, "max": 100.0, "step":0.01, "round": False}),
                    }
               }
    RETURN_TYPES = ("SIGMAS",)
    CATEGORY = "sampling/custom_sampling/schedulers"

    FUNCTION = "get_sigmas"

    def get_sigmas(self, steps, sigma_max, sigma_min, rho):
        sigmas = k_diffusion_sampling.get_sigmas_karras(n=steps, sigma_min=sigma_min, sigma_max=sigma_max, rho=rho)
        return (sigmas, )

```
