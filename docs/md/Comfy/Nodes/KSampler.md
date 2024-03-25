# KSampler
## Documentation
- Class name: `KSampler`
- Category: `sampling`
- Output node: `False`

The KSampler node is designed for advanced sampling operations within generative models, allowing for the customization of sampling processes through various parameters. It facilitates the generation of new data samples by manipulating latent space representations, leveraging conditioning, and adjusting noise levels.
## Input types
### Required
- **`model`**
    - Comfy dtype: `MODEL`
    - Specifies the generative model to be used for sampling, playing a crucial role in determining the characteristics of the generated samples.
    - Python dtype: `torch.nn.Module`
- **`seed`**
    - Comfy dtype: `INT`
    - Controls the randomness of the sampling process, ensuring reproducibility of results when set to a specific value.
    - Python dtype: `int`
- **`steps`**
    - Comfy dtype: `INT`
    - Determines the number of steps to be taken in the sampling process, affecting the detail and quality of the generated samples.
    - Python dtype: `int`
- **`cfg`**
    - Comfy dtype: `FLOAT`
    - Adjusts the conditioning factor, influencing the direction and strength of the conditioning applied during sampling.
    - Python dtype: `float`
- **`sampler_name`**
    - Comfy dtype: `COMBO[STRING]`
    - Selects the specific sampling algorithm to be used, impacting the behavior and outcome of the sampling process.
    - Python dtype: `str`
- **`scheduler`**
    - Comfy dtype: `COMBO[STRING]`
    - Chooses the scheduling algorithm for controlling the sampling process, affecting the progression and dynamics of sampling.
    - Python dtype: `str`
- **`positive`**
    - Comfy dtype: `CONDITIONING`
    - Defines positive conditioning to guide the sampling towards desired attributes or features.
    - Python dtype: `dict`
- **`negative`**
    - Comfy dtype: `CONDITIONING`
    - Specifies negative conditioning to steer the sampling away from certain attributes or features.
    - Python dtype: `dict`
- **`latent_image`**
    - Comfy dtype: `LATENT`
    - Provides a latent space representation to be used as a starting point or reference for the sampling process.
    - Python dtype: `torch.Tensor`
- **`denoise`**
    - Comfy dtype: `FLOAT`
    - Controls the level of denoising applied to the samples, affecting the clarity and sharpness of the generated images.
    - Python dtype: `float`
## Output types
- **`latent`**
    - Comfy dtype: `LATENT`
    - Represents the latent space output of the sampling process, encapsulating the generated samples.
    - Python dtype: `torch.Tensor`
## Usage tips
- Infra type: `GPU`
- Common nodes: `VAEDecode,LatentUpscaleBy,VAEDecodeTiled,Reroute,LatentComposite,NNLatentUpscale,LatentUpscale,KSampler,workflow/front,SetNode`


## Source code
```python
class KSampler:
    @classmethod
    def INPUT_TYPES(s):
        return {"required":
                    {"model": ("MODEL",),
                    "seed": ("INT", {"default": 0, "min": 0, "max": 0xffffffffffffffff}),
                    "steps": ("INT", {"default": 20, "min": 1, "max": 10000}),
                    "cfg": ("FLOAT", {"default": 8.0, "min": 0.0, "max": 100.0, "step":0.1, "round": 0.01}),
                    "sampler_name": (comfy.samplers.KSampler.SAMPLERS, ),
                    "scheduler": (comfy.samplers.KSampler.SCHEDULERS, ),
                    "positive": ("CONDITIONING", ),
                    "negative": ("CONDITIONING", ),
                    "latent_image": ("LATENT", ),
                    "denoise": ("FLOAT", {"default": 1.0, "min": 0.0, "max": 1.0, "step": 0.01}),
                     }
                }

    RETURN_TYPES = ("LATENT",)
    FUNCTION = "sample"

    CATEGORY = "sampling"

    def sample(self, model, seed, steps, cfg, sampler_name, scheduler, positive, negative, latent_image, denoise=1.0):
        return common_ksampler(model, seed, steps, cfg, sampler_name, scheduler, positive, negative, latent_image, denoise=denoise)

```
