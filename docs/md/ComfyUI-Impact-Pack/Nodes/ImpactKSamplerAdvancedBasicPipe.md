# KSampler (Advanced/pipe)
## Documentation
- Class name: `ImpactKSamplerAdvancedBasicPipe`
- Category: `sampling`
- Output node: `False`

This node provides an advanced sampling mechanism by wrapping around the KSamplerAdvanced class. It configures the sampler with specific parameters such as configuration (cfg), sampler name, scheduler, and the basic pipeline components. The node is designed for more complex sampling scenarios where advanced control over the sampling process is required.
## Input types
### Required
- **`basic_pipe`**
    - Provides the basic components required for the sampling process, including the model and positive/negative conditioning. It serves as the foundation for the sampling operation.
    - Python dtype: `Tuple[torch.nn.Module, NoneType, NoneType, List[str], List[str]]`
    - Comfy dtype: `BASIC_PIPE`
- **`add_noise`**
    - Determines whether noise should be added to the sampling process, enhancing the diversity of the generated samples.
    - Python dtype: `bool`
    - Comfy dtype: `BOOLEAN`
- **`noise_seed`**
    - Specifies the seed for noise generation, ensuring reproducibility in the sampling process when adding noise.
    - Python dtype: `int`
    - Comfy dtype: `INT`
- **`steps`**
    - Defines the number of steps in the sampling process, affecting the detail and quality of the generated samples.
    - Python dtype: `int`
    - Comfy dtype: `INT`
- **`cfg`**
    - Specifies the configuration for the sampling process, affecting aspects like sampling precision and behavior.
    - Python dtype: `float`
    - Comfy dtype: `FLOAT`
- **`sampler_name`**
    - Determines the specific sampler algorithm to be used during the sampling process. This choice influences the sampling strategy and outcomes.
    - Python dtype: `str`
    - Comfy dtype: `STRING`
- **`scheduler`**
    - Selects the scheduling algorithm for controlling the sampling steps. The scheduler affects the progression and adjustment of sampling parameters over time.
    - Python dtype: `str`
    - Comfy dtype: `STRING`
- **`latent_image`**
    - unknown
    - Python dtype: `unknown`
    - Comfy dtype: `LATENT`
- **`start_at_step`**
    - unknown
    - Python dtype: `unknown`
    - Comfy dtype: `INT`
- **`end_at_step`**
    - unknown
    - Python dtype: `unknown`
    - Comfy dtype: `INT`
- **`return_with_leftover_noise`**
    - unknown
    - Python dtype: `unknown`
    - Comfy dtype: `BOOLEAN`
## Output types
- **`basic_pipe`**
    - Returns the basic pipeline components, including the model and conditioning information.
    - Python dtype: `Tuple[torch.nn.Module, NoneType, NoneType, List[str], List[str]]`
    - Comfy dtype: `BASIC_PIPE`
- **`latent`**
    - The generated latent representation as a result of the advanced sampling process.
    - Python dtype: `torch.Tensor`
    - Comfy dtype: `LATENT`
- **`vae`**
    - The variational autoencoder used in the sampling process, facilitating the generation of detailed samples.
    - Python dtype: `torch.nn.Module`
    - Comfy dtype: `VAE`
## Usage tips
- Infra type: `GPU`
- Common nodes: unknown


## Source code
```python
class KSamplerAdvancedBasicPipe:
    @classmethod
    def INPUT_TYPES(s):
        return {"required":
                    {"basic_pipe": ("BASIC_PIPE",),
                     "add_noise": ("BOOLEAN", {"default": True, "label_on": "enable", "label_off": "disable"}),
                     "noise_seed": ("INT", {"default": 0, "min": 0, "max": 0xffffffffffffffff}),
                     "steps": ("INT", {"default": 20, "min": 1, "max": 10000}),
                     "cfg": ("FLOAT", {"default": 8.0, "min": 0.0, "max": 100.0}),
                     "sampler_name": (comfy.samplers.KSampler.SAMPLERS, ),
                     "scheduler": (comfy.samplers.KSampler.SCHEDULERS, ),
                     "latent_image": ("LATENT", ),
                     "start_at_step": ("INT", {"default": 0, "min": 0, "max": 10000}),
                     "end_at_step": ("INT", {"default": 10000, "min": 0, "max": 10000}),
                     "return_with_leftover_noise": ("BOOLEAN", {"default": False, "label_on": "enable", "label_off": "disable"}),
                     }
                }

    RETURN_TYPES = ("BASIC_PIPE", "LATENT", "VAE")
    FUNCTION = "sample"

    CATEGORY = "sampling"

    def sample(self, basic_pipe, add_noise, noise_seed, steps, cfg, sampler_name, scheduler, latent_image, start_at_step, end_at_step, return_with_leftover_noise, denoise=1.0):
        model, clip, vae, positive, negative = basic_pipe

        if add_noise:
            add_noise = "enable"
        else:
            add_noise = "disable"

        if return_with_leftover_noise:
            return_with_leftover_noise = "enable"
        else:
            return_with_leftover_noise = "disable"

        latent = nodes.KSamplerAdvanced().sample(model, add_noise, noise_seed, steps, cfg, sampler_name, scheduler, positive, negative, latent_image, start_at_step, end_at_step, return_with_leftover_noise, denoise)[0]
        return basic_pipe, latent, vae

```
