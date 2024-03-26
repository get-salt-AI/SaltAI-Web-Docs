# KSampler (WAS)
## Documentation
- Class name: `KSampler`
- Category: `WAS Suite/Sampling`
- Output node: `False`

The KSampler node is designed for advanced sampling in generative models, allowing for the customization of the sampling process through various parameters. It integrates with the broader WAS Suite to provide a flexible and powerful tool for generating and manipulating latent representations.
## Input types
### Required
- **`model`**
    - The generative model used for sampling. It's crucial for defining the behavior and output of the sampling process.
    - Comfy dtype: `MODEL`
    - Python dtype: `torch.nn.Module`
- **`seed`**
    - A seed value to ensure reproducibility of the sampling results. It influences the randomness of the generated samples.
    - Comfy dtype: `SEED`
    - Python dtype: `int`
- **`steps`**
    - The number of steps to perform in the sampling process, affecting the detail and quality of the generated samples.
    - Comfy dtype: `INT`
    - Python dtype: `int`
- **`cfg`**
    - A configuration parameter that adjusts the sampling process, allowing for fine-tuning of the generation.
    - Comfy dtype: `FLOAT`
    - Python dtype: `float`
- **`sampler_name`**
    - Specifies the particular sampler to use, enabling the selection of different sampling strategies.
    - Comfy dtype: `COMBO[STRING]`
    - Python dtype: `str`
- **`scheduler`**
    - Determines the scheduling algorithm for the sampling process, impacting how samples evolve over time.
    - Comfy dtype: `COMBO[STRING]`
    - Python dtype: `str`
- **`positive`**
    - Positive conditioning to guide the sampling towards desired attributes.
    - Comfy dtype: `CONDITIONING`
    - Python dtype: `str`
- **`negative`**
    - Negative conditioning to steer the sampling away from certain attributes.
    - Comfy dtype: `CONDITIONING`
    - Python dtype: `str`
- **`latent_image`**
    - An optional latent image to start the sampling from, allowing for modifications or enhancements of existing images.
    - Comfy dtype: `LATENT`
    - Python dtype: `torch.Tensor`
- **`denoise`**
    - Controls the denoising level in the sampling process, affecting the clarity and sharpness of the generated samples.
    - Comfy dtype: `FLOAT`
    - Python dtype: `float`
## Output types
- **`latent`**
    - Comfy dtype: `LATENT`
    - The output latent representation generated by the sampling process, which can be further used or transformed.
    - Python dtype: `torch.Tensor`
## Usage tips
- Infra type: `GPU`
- Common nodes: `VAEDecode,LatentUpscaleBy,VAEDecodeTiled,Reroute,LatentComposite,NNLatentUpscale,LatentUpscale,KSampler,workflow/front,SetNode`


## Source code
```python
class WAS_KSampler:
    @classmethod
    def INPUT_TYPES(cls):
        return {"required":

                {"model": ("MODEL", ),
                 "seed": ("SEED", ),
                 "steps": ("INT", {"default": 20, "min": 1, "max": 10000}),
                 "cfg": ("FLOAT", {"default": 8.0, "min": 0.0, "max": 100.0}),
                 "sampler_name": (comfy.samplers.KSampler.SAMPLERS, ),
                 "scheduler": (comfy.samplers.KSampler.SCHEDULERS, ),
                 "positive": ("CONDITIONING", ),
                 "negative": ("CONDITIONING", ),
                 "latent_image": ("LATENT", ),
                 "denoise": ("FLOAT", {"default": 1.0, "min": 0.0, "max": 1.0, "step": 0.01}),
                 }}

    RETURN_TYPES = ("LATENT",)
    FUNCTION = "sample"

    CATEGORY = "WAS Suite/Sampling"

    def sample(self, model, seed, steps, cfg, sampler_name, scheduler, positive, negative, latent_image, denoise=1.0):
        return nodes.common_ksampler(model, seed['seed'], steps, cfg, sampler_name, scheduler, positive, negative, latent_image, denoise=denoise)

```