# KSampler
## Documentation
- Class name: `KSampler`
- Category: `sampling`
- Output node: `False`

The `KSampler` node is designed for sampling operations using a variety of samplers and schedulers. It allows for the generation of new samples based on the provided model, seed, steps, and other parameters. This node is capable of handling different types of conditioning, adjusting the noise level, and utilizing specific samplers and schedulers to influence the generation process.
## Input types
### Required
- **`model`**
    - Specifies the model to be used for sampling. The choice of model affects the quality and characteristics of the generated samples.
    - Python dtype: `torch.nn.Module`
    - Comfy dtype: `MODEL`
- **`seed`**
    - Determines the random seed for generating samples, ensuring reproducibility of results.
    - Python dtype: `int`
    - Comfy dtype: `INT`
- **`steps`**
    - Defines the number of steps to be taken in the sampling process, impacting the detail and quality of the generated samples.
    - Python dtype: `int`
    - Comfy dtype: `INT`
- **`cfg`**
    - Controls the conditioning factor, influencing the diversity and fidelity of the generated samples.
    - Python dtype: `float`
    - Comfy dtype: `FLOAT`
- **`sampler_name`**
    - Selects the specific sampler to be used, affecting the sampling strategy and outcome.
    - Python dtype: `str`
    - Comfy dtype: `['euler', 'euler_ancestral', 'heun'...]`
- **`scheduler`**
    - Chooses the scheduler for controlling the sampling process, impacting the progression and quality of samples.
    - Python dtype: `str`
    - Comfy dtype: `['normal', 'karras', 'exponential'...]`
- **`positive`**
    - Provides positive conditioning to guide the sampling towards desired attributes.
    - Python dtype: `Dict[str, torch.Tensor]`
    - Comfy dtype: `CONDITIONING`
- **`negative`**
    - Provides negative conditioning to steer the sampling away from undesired attributes.
    - Python dtype: `Dict[str, torch.Tensor]`
    - Comfy dtype: `CONDITIONING`
- **`latent_image`**
    - The initial latent image to be modified through the sampling process.
    - Python dtype: `torch.Tensor`
    - Comfy dtype: `LATENT`
- **`denoise`**
    - Adjusts the level of denoising applied to the samples, affecting their clarity and detail.
    - Python dtype: `float`
    - Comfy dtype: `FLOAT`
## Output types
- **`latent`**
    - The generated samples after the sampling process, represented in latent space.
    - Python dtype: `torch.Tensor`
    - Comfy dtype: `LATENT`
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
