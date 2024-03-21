# ExponentialScheduler
## Documentation
- Class name: `ExponentialScheduler`
- Category: `sampling/custom_sampling/schedulers`
- Output node: `False`

The ExponentialScheduler node generates a sequence of sigma values following an exponential schedule for diffusion sampling. It calculates these values based on the number of steps and the minimum and maximum sigma values provided.
## Input types
### Required
- **`steps`**
    - Defines the total number of steps for the diffusion process. The number of steps influences the granularity of the generated sigma sequence, with more steps resulting in a finer sequence.
    - Python dtype: `int`
    - Comfy dtype: `INT`
- **`sigma_max`**
    - The maximum sigma value in the generated sequence. It sets the upper limit of noise levels for the diffusion process.
    - Python dtype: `float`
    - Comfy dtype: `FLOAT`
- **`sigma_min`**
    - The minimum sigma value in the generated sequence. It sets the lower limit of noise levels, influencing the starting point of the diffusion process.
    - Python dtype: `float`
    - Comfy dtype: `FLOAT`
## Output types
- **`sigmas`**
    - The generated sequence of sigma values following an exponential schedule. These values are used to control the noise levels at each step of the diffusion process.
    - Python dtype: `Tuple[torch.Tensor]`
    - Comfy dtype: `SIGMAS`
## Usage tips
- Infra type: `GPU`
- Common nodes: unknown


## Source code
```python
class ExponentialScheduler:
    @classmethod
    def INPUT_TYPES(s):
        return {"required":
                    {"steps": ("INT", {"default": 20, "min": 1, "max": 10000}),
                     "sigma_max": ("FLOAT", {"default": 14.614642, "min": 0.0, "max": 1000.0, "step":0.01, "round": False}),
                     "sigma_min": ("FLOAT", {"default": 0.0291675, "min": 0.0, "max": 1000.0, "step":0.01, "round": False}),
                    }
               }
    RETURN_TYPES = ("SIGMAS",)
    CATEGORY = "sampling/custom_sampling/schedulers"

    FUNCTION = "get_sigmas"

    def get_sigmas(self, steps, sigma_max, sigma_min):
        sigmas = k_diffusion_sampling.get_sigmas_exponential(n=steps, sigma_min=sigma_min, sigma_max=sigma_max)
        return (sigmas, )

```
