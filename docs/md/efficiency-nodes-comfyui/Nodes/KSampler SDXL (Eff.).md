---
tags:
- Sampling
---

# KSampler SDXL (Eff.)
## Documentation
- Class name: `KSampler SDXL (Eff.)`
- Category: `Efficiency Nodes/Sampling`
- Output node: `True`

This node specializes in sampling images using a sophisticated version of the KSampler algorithm, optimized for efficiency and tailored for use with the SDXL model. It incorporates advanced features such as noise addition, step refinement, and optional VAE decoding, making it suitable for generating high-quality images with fine-tuned control over the generation process.
## Input types
### Required
- **`sdxl_tuple`**
    - Represents the model used for sampling, encapsulating the necessary parameters and configurations specific to the SDXL model.
    - Comfy dtype: `SDXL_TUPLE`
    - Python dtype: `tuple`
- **`noise_seed`**
    - The seed used for generating noise, ensuring reproducibility and consistency in the sampling process.
    - Comfy dtype: `INT`
    - Python dtype: `int`
- **`steps`**
    - The number of steps to iterate the sampling process, affecting the detail and quality of the generated images.
    - Comfy dtype: `INT`
    - Python dtype: `int`
- **`cfg`**
    - The conditioning factor guiding the sampling process, influencing the adherence to the provided prompts or images.
    - Comfy dtype: `FLOAT`
    - Python dtype: `float`
- **`sampler_name`**
    - The identifier for the specific KSampler variant used, in this case, tailored for the SDXL model.
    - Comfy dtype: `COMBO[STRING]`
    - Python dtype: `str`
- **`scheduler`**
    - The scheduling algorithm applied to control the sampling process, optimizing the balance between speed and quality.
    - Comfy dtype: `COMBO[STRING]`
    - Python dtype: `str`
- **`latent_image`**
    - The initial latent image to be refined or altered through the sampling process, serving as a starting point.
    - Comfy dtype: `LATENT`
    - Python dtype: `torch.Tensor`
- **`start_at_step`**
    - The step at which to begin the sampling process, allowing for mid-process intervention or refinement.
    - Comfy dtype: `INT`
    - Python dtype: `int`
- **`refine_at_step`**
    - Specifies the step at which to apply refinement operations, enhancing the final image quality.
    - Comfy dtype: `INT`
    - Python dtype: `int`
- **`preview_method`**
    - The method used for generating previews during the sampling process, aiding in visualization and adjustment.
    - Comfy dtype: `COMBO[STRING]`
    - Python dtype: `str`
- **`vae_decode`**
    - Indicates whether to use a VAE decoder for post-processing the sampled images, affecting the final image style.
    - Comfy dtype: `COMBO[STRING]`
    - Python dtype: `bool`
### Optional
- **`optional_vae`**
    - An optional VAE model for further processing the sampled images, offering an additional layer of customization.
    - Comfy dtype: `VAE`
    - Python dtype: `tuple`
- **`script`**
    - A script for further customizing the sampling process, enabling complex generation scenarios.
    - Comfy dtype: `SCRIPT`
    - Python dtype: `str`
## Output types
- **`SDXL_TUPLE`**
    - Comfy dtype: `SDXL_TUPLE`
    - The SDXL model tuple, encapsulating the model and its configurations used in the sampling process.
    - Python dtype: `tuple`
- **`LATENT`**
    - Comfy dtype: `LATENT`
    - The latent representation of the sampled image, serving as an intermediate step in the image generation process.
    - Python dtype: `torch.Tensor`
- **`VAE`**
    - Comfy dtype: `VAE`
    - The optional VAE model used for further processing the sampled images, enhancing their quality and style.
    - Python dtype: `tuple`
- **`IMAGE`**
    - Comfy dtype: `IMAGE`
    - The final output image generated by the sampling process, showcasing the result of the applied configurations and models.
    - Python dtype: `torch.Tensor`
## Usage tips
- Infra type: `GPU`
- Common nodes:
    - [SaveImage](../../Comfy/Nodes/SaveImage.md)
    - [PreviewImage](../../Comfy/Nodes/PreviewImage.md)
    - [Image Filter Adjustments](../../was-node-suite-comfyui/Nodes/Image Filter Adjustments.md)
    - [IterativeLatentUpscale](../../ComfyUI-Impact-Pack/Nodes/IterativeLatentUpscale.md)



## Source code
```python
class TSC_KSamplerSDXL(TSC_KSampler):

    @classmethod
    def INPUT_TYPES(cls):
        return {"required":
                    {"sdxl_tuple": ("SDXL_TUPLE",),
                     "noise_seed": ("INT", {"default": 0, "min": 0, "max": 0xffffffffffffffff}),
                     "steps": ("INT", {"default": 20, "min": 1, "max": 10000}),
                     "cfg": ("FLOAT", {"default": 7.0, "min": 0.0, "max": 100.0}),
                     "sampler_name": (comfy.samplers.KSampler.SAMPLERS,),
                     "scheduler": (SCHEDULERS,),
                     "latent_image": ("LATENT",),
                     "start_at_step": ("INT", {"default": 0, "min": 0, "max": 10000}),
                     "refine_at_step": ("INT", {"default": -1, "min": -1, "max": 10000}),
                     "preview_method": (["auto", "latent2rgb", "taesd", "none"],),
                     "vae_decode": (["true", "true (tiled)", "false", "output only", "output only (tiled)"],),
                     },
                "optional": {"optional_vae": ("VAE",),
                             "script": ("SCRIPT",),},
                "hidden": {"prompt": "PROMPT", "extra_pnginfo": "EXTRA_PNGINFO", "my_unique_id": "UNIQUE_ID",},
                }

    RETURN_TYPES = ("SDXL_TUPLE", "LATENT", "VAE", "IMAGE",)
    RETURN_NAMES = ("SDXL_TUPLE", "LATENT", "VAE", "IMAGE",)
    OUTPUT_NODE = True
    FUNCTION = "sample_sdxl"
    CATEGORY = "Efficiency Nodes/Sampling"

    def sample_sdxl(self, sdxl_tuple, noise_seed, steps, cfg, sampler_name, scheduler, latent_image,
               start_at_step, refine_at_step, preview_method, vae_decode, prompt=None, extra_pnginfo=None,
               my_unique_id=None, optional_vae=(None,), refiner_extras=None, script=None):
        # sdxl_tuple sent through the 'model' channel
        negative = None
        return super().sample(sdxl_tuple, noise_seed, steps, cfg, sampler_name, scheduler,
               refiner_extras, negative, latent_image, preview_method, vae_decode, denoise=1.0,
               prompt=prompt, extra_pnginfo=extra_pnginfo, my_unique_id=my_unique_id, optional_vae=optional_vae,
               script=script, add_noise=None, start_at_step=start_at_step, end_at_step=refine_at_step,
               return_with_leftover_noise=None,sampler_type="sdxl")

```
