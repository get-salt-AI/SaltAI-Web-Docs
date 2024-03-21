# SDTurboScheduler
## Documentation
- Class name: `SDTurboScheduler`
- Category: `sampling/custom_sampling/schedulers`
- Output node: `False`

The `SDTurboScheduler` node is designed to generate a sequence of sigma values for diffusion models, specifically tailored to adjust the noise level based on the `denoise` parameter and the number of steps. It calculates a specific range of timesteps and uses the model's sigma function to produce the sigma values, which are crucial for controlling the noise level during the diffusion process.
## Input types
### Required
- **`model`**
    - The model parameter is crucial as it specifies the diffusion model for which the sigma values are to be generated. The choice of model affects the characteristics of the generated sigmas, directly influencing the noise control in the diffusion process.
    - Python dtype: `torch.nn.Module`
    - Comfy dtype: `MODEL`
- **`steps`**
    - The steps parameter determines the length of the sigma sequence to be generated. It directly influences the granularity of the noise control during the diffusion process.
    - Python dtype: `int`
    - Comfy dtype: `INT`
- **`denoise`**
    - The denoise parameter adjusts the starting point of the sigma sequence, allowing for finer control over the noise level in the generated images. It essentially modifies the range of timesteps used to calculate the sigmas.
    - Python dtype: `float`
    - Comfy dtype: `FLOAT`
## Output types
- **`sigmas`**
    - The output sigmas are a sequence of values crucial for controlling the noise level during the diffusion process. They are calculated based on the model's sigma function and the specified timesteps.
    - Python dtype: `torch.Tensor`
    - Comfy dtype: `SIGMAS`
## Usage tips
- Infra type: `GPU`
- Common nodes: `SamplerCustom,SplitSigmas`


## Source code
```python
class SDTurboScheduler:
    @classmethod
    def INPUT_TYPES(s):
        return {"required":
                    {"model": ("MODEL",),
                     "steps": ("INT", {"default": 1, "min": 1, "max": 10}),
                     "denoise": ("FLOAT", {"default": 1.0, "min": 0, "max": 1.0, "step": 0.01}),
                      }
               }
    RETURN_TYPES = ("SIGMAS",)
    CATEGORY = "sampling/custom_sampling/schedulers"

    FUNCTION = "get_sigmas"

    def get_sigmas(self, model, steps, denoise):
        start_step = 10 - int(10 * denoise)
        timesteps = torch.flip(torch.arange(1, 11) * 100 - 1, (0,))[start_step:start_step + steps]
        comfy.model_management.load_models_gpu([model])
        sigmas = model.model.model_sampling.sigma(timesteps)
        sigmas = torch.cat([sigmas, sigmas.new_zeros([1])])
        return (sigmas, )

```
