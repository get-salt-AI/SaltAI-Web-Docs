# VPScheduler
## Documentation
- Class name: `VPScheduler`
- Category: `sampling/custom_sampling/schedulers`
- Output node: `False`

The VPScheduler node is designed to generate a sequence of noise levels (sigmas) based on the Variance Preserving (VP) SDE approach. It calculates these noise levels using specific parameters that influence the diffusion process, making it a crucial component for custom sampling strategies in generative models.
## Input types
### Required
- **`steps`**
    - Defines the number of steps in the diffusion process, directly affecting the granularity of the generated noise levels.
    - Python dtype: `int`
    - Comfy dtype: `INT`
- **`beta_d`**
    - Specifies the beta decay parameter, influencing the rate at which noise levels decrease throughout the diffusion steps.
    - Python dtype: `float`
    - Comfy dtype: `FLOAT`
- **`beta_min`**
    - Sets the minimum beta value, determining the lowest noise level achievable in the diffusion process.
    - Python dtype: `float`
    - Comfy dtype: `FLOAT`
- **`eps_s`**
    - Controls the epsilon scale, a parameter that fine-tunes the noise levels for more precise control over the diffusion process.
    - Python dtype: `float`
    - Comfy dtype: `FLOAT`
## Output types
- **`sigmas`**
    - Outputs a sequence of noise levels (sigmas) calculated based on the input parameters, essential for guiding the diffusion process in generative models.
    - Python dtype: `Tuple[torch.Tensor]`
    - Comfy dtype: `SIGMAS`
## Usage tips
- Infra type: `GPU`
- Common nodes: unknown


## Source code
```python
class VPScheduler:
    @classmethod
    def INPUT_TYPES(s):
        return {"required":
                    {"steps": ("INT", {"default": 20, "min": 1, "max": 10000}),
                     "beta_d": ("FLOAT", {"default": 19.9, "min": 0.0, "max": 1000.0, "step":0.01, "round": False}), #TODO: fix default values
                     "beta_min": ("FLOAT", {"default": 0.1, "min": 0.0, "max": 1000.0, "step":0.01, "round": False}),
                     "eps_s": ("FLOAT", {"default": 0.001, "min": 0.0, "max": 1.0, "step":0.0001, "round": False}),
                    }
               }
    RETURN_TYPES = ("SIGMAS",)
    CATEGORY = "sampling/custom_sampling/schedulers"

    FUNCTION = "get_sigmas"

    def get_sigmas(self, steps, beta_d, beta_min, eps_s):
        sigmas = k_diffusion_sampling.get_sigmas_vp(n=steps, beta_d=beta_d, beta_min=beta_min, eps_s=eps_s)
        return (sigmas, )

```
