# KSampler (pipe)
## Documentation
- Class name: `ImpactKSamplerBasicPipe`
- Category: `sampling`
- Output node: `False`

The `ImpactKSamplerBasicPipe` node is designed for sampling operations within a basic pipeline. It utilizes a specific model, seed, number of steps, configuration, sampler name, scheduler, latent image, and denoise factor to generate a new latent representation. This process involves the use of a KSampler to perform the sampling based on the provided parameters and the content of the basic pipeline, which includes model, clip, VAE, and positive and negative prompts.
## Input types
### Required
- **`basic_pipe`**
    - Represents the basic pipeline components including model, clip, VAE, and positive and negative prompts, which are essential for the sampling process.
    - Python dtype: `Tuple[torch.nn.Module, Any, Any, List[str], List[str]]`
    - Comfy dtype: `BASIC_PIPE`
- **`seed`**
    - The seed value is used to ensure reproducibility of the sampling process. It directly influences the randomness of the sampling, affecting the uniqueness and variation of the generated latent image.
    - Python dtype: `int`
    - Comfy dtype: `INT`
- **`steps`**
    - Defines the number of steps to be taken in the sampling process, affecting the detail and quality of the generated latent image.
    - Python dtype: `int`
    - Comfy dtype: `INT`
- **`cfg`**
    - Configuration parameter that influences the behavior of the sampling process. It can adjust the sampling intensity, impacting the overall appearance and characteristics of the generated latent image.
    - Python dtype: `float`
    - Comfy dtype: `FLOAT`
- **`sampler_name`**
    - Specifies the name of the sampler to be used, which determines the sampling algorithm. Different samplers can lead to variations in the sampling process and the final image quality.
    - Python dtype: `str`
    - Comfy dtype: `STRING`
- **`scheduler`**
    - Determines the scheduling algorithm used during the sampling process. The choice of scheduler can affect the progression of sampling steps, potentially influencing the final image's fidelity and coherence.
    - Python dtype: `str`
    - Comfy dtype: `STRING`
- **`latent_image`**
    - The initial latent image that serves as a starting point for the sampling process.
    - Python dtype: `torch.Tensor`
    - Comfy dtype: `LATENT`
- **`denoise`**
    - A factor that influences the amount of denoising applied during the sampling process. Adjusting this parameter can help in reducing noise and improving the clarity of the generated image.
    - Python dtype: `float`
    - Comfy dtype: `FLOAT`
## Output types
- **`basic_pipe`**
    - Returns the basic pipeline components unchanged.
    - Python dtype: `Tuple[torch.nn.Module, Any, Any, List[str], List[str]]`
    - Comfy dtype: `BASIC_PIPE`
- **`latent`**
    - The newly generated latent representation as a result of the sampling process.
    - Python dtype: `torch.Tensor`
    - Comfy dtype: `LATENT`
- **`vae`**
    - Returns the VAE component of the basic pipeline unchanged.
    - Python dtype: `Any`
    - Comfy dtype: `VAE`
## Usage tips
- Infra type: `GPU`
- Common nodes: `VAEDecode,Reroute,ImpactKSamplerBasicPipe,VAEEncode`


## Source code
```python
class KSamplerBasicPipe:
    @classmethod
    def INPUT_TYPES(s):
        return {"required":
                    {"basic_pipe": ("BASIC_PIPE",),
                     "seed": ("INT", {"default": 0, "min": 0, "max": 0xffffffffffffffff}),
                     "steps": ("INT", {"default": 20, "min": 1, "max": 10000}),
                     "cfg": ("FLOAT", {"default": 8.0, "min": 0.0, "max": 100.0}),
                     "sampler_name": (comfy.samplers.KSampler.SAMPLERS, ),
                     "scheduler": (comfy.samplers.KSampler.SCHEDULERS, ),
                     "latent_image": ("LATENT", ),
                     "denoise": ("FLOAT", {"default": 1.0, "min": 0.0, "max": 1.0, "step": 0.01}),
                     }
                }

    RETURN_TYPES = ("BASIC_PIPE", "LATENT", "VAE")
    FUNCTION = "sample"

    CATEGORY = "sampling"

    def sample(self, basic_pipe, seed, steps, cfg, sampler_name, scheduler, latent_image, denoise=1.0):
        model, clip, vae, positive, negative = basic_pipe
        latent = nodes.KSampler().sample(model, seed, steps, cfg, sampler_name, scheduler, positive, negative, latent_image, denoise)[0]
        return basic_pipe, latent, vae

```
