# BasicScheduler
## Documentation
- Class name: `BasicScheduler`
- Category: `sampling/custom_sampling/schedulers`
- Output node: `False`

The BasicScheduler node calculates a sequence of sigma values for diffusion models based on the provided scheduler, model, steps, and denoise parameters. It adjusts the total number of steps based on the denoise factor and utilizes the model's scheduler to generate the sigma values.
## Input types
### Required
- **`model`**
    - The model parameter specifies the diffusion model for which the sigma values are to be calculated. It is crucial for determining the behavior of the diffusion process.
    - Python dtype: `comfy.models.DiffusionModel`
    - Comfy dtype: `MODEL`
- **`scheduler`**
    - The scheduler parameter determines the method used to calculate the sigma values. It affects the diffusion process by altering the noise levels at each step.
    - Python dtype: `str`
    - Comfy dtype: `STRING`
- **`steps`**
    - Specifies the number of diffusion steps. It directly influences the granularity of the diffusion process.
    - Python dtype: `int`
    - Comfy dtype: `INT`
- **`denoise`**
    - A factor that adjusts the total number of steps based on its value, affecting the smoothness of the diffusion process.
    - Python dtype: `float`
    - Comfy dtype: `FLOAT`
## Output types
- **`sigmas`**
    - A sequence of sigma values calculated for the diffusion model. These values are essential for controlling the noise level throughout the diffusion process.
    - Python dtype: `torch.Tensor`
    - Comfy dtype: `SIGMAS`
## Usage tips
- Infra type: `GPU`
- Common nodes: `SamplerCustom,SplitSigmas,Reroute`


## Source code
```python
class BasicScheduler:
    @classmethod
    def INPUT_TYPES(s):
        return {"required":
                    {"model": ("MODEL",),
                     "scheduler": (comfy.samplers.SCHEDULER_NAMES, ),
                     "steps": ("INT", {"default": 20, "min": 1, "max": 10000}),
                     "denoise": ("FLOAT", {"default": 1.0, "min": 0.0, "max": 1.0, "step": 0.01}),
                      }
               }
    RETURN_TYPES = ("SIGMAS",)
    CATEGORY = "sampling/custom_sampling/schedulers"

    FUNCTION = "get_sigmas"

    def get_sigmas(self, model, scheduler, steps, denoise):
        total_steps = steps
        if denoise < 1.0:
            total_steps = int(steps/denoise)

        comfy.model_management.load_models_gpu([model])
        sigmas = comfy.samplers.calculate_sigmas_scheduler(model.model, scheduler, total_steps).cpu()
        sigmas = sigmas[-(steps + 1):]
        return (sigmas, )

```
