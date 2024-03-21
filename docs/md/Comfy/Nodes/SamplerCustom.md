# SamplerCustom
## Documentation
- Class name: `SamplerCustom`
- Category: `sampling/custom_sampling`
- Output node: `False`

This node represents a custom sampling process, which involves generating or modifying latent images based on various parameters such as model, conditioning, and specific sampler configurations. It's designed to work with different types of samplers and configurations to produce customized outputs.
## Input types
### Required
- **`model`**
    - Specifies the generative model used for sampling. It's crucial for determining the behavior and capabilities of the sampling process.
    - Python dtype: `torch.nn.Module`
    - Comfy dtype: `MODEL`
- **`add_noise`**
    - Determines whether noise should be added to the sampling process, affecting the randomness and variability of the output.
    - Python dtype: `bool`
    - Comfy dtype: `BOOLEAN`
- **`noise_seed`**
    - Sets the seed for noise generation, ensuring reproducibility of the results when adding noise.
    - Python dtype: `int`
    - Comfy dtype: `INT`
- **`cfg`**
    - Controls the conditioning factor, influencing the strength of the conditioning on the sampling process.
    - Python dtype: `float`
    - Comfy dtype: `FLOAT`
- **`positive`**
    - Positive conditioning to guide the sampling towards desired attributes or features.
    - Python dtype: `dict`
    - Comfy dtype: `CONDITIONING`
- **`negative`**
    - Negative conditioning to steer the sampling away from certain attributes or features.
    - Python dtype: `dict`
    - Comfy dtype: `CONDITIONING`
- **`sampler`**
    - The specific sampler algorithm used for generating or modifying the latent images.
    - Python dtype: `str`
    - Comfy dtype: `SAMPLER`
- **`sigmas`**
    - Specifies the noise levels for each step of the sampling process, affecting the detail and quality of the output.
    - Python dtype: `torch.Tensor`
    - Comfy dtype: `SIGMAS`
- **`latent_image`**
    - The initial latent image to be modified or used as a basis for generation in the sampling process.
    - Python dtype: `dict`
    - Comfy dtype: `LATENT`
## Output types
- **`latent`**
    - The denoised version of the output latent image, providing a cleaner result.
    - Python dtype: `dict`
    - Comfy dtype: `LATENT`
## Usage tips
- Infra type: `GPU`
- Common nodes: `VAEDecode,Preview Chooser,LatentUpscaleBy`


## Source code
```python
class SamplerCustom:
    @classmethod
    def INPUT_TYPES(s):
        return {"required":
                    {"model": ("MODEL",),
                    "add_noise": ("BOOLEAN", {"default": True}),
                    "noise_seed": ("INT", {"default": 0, "min": 0, "max": 0xffffffffffffffff}),
                    "cfg": ("FLOAT", {"default": 8.0, "min": 0.0, "max": 100.0, "step":0.1, "round": 0.01}),
                    "positive": ("CONDITIONING", ),
                    "negative": ("CONDITIONING", ),
                    "sampler": ("SAMPLER", ),
                    "sigmas": ("SIGMAS", ),
                    "latent_image": ("LATENT", ),
                     }
                }

    RETURN_TYPES = ("LATENT","LATENT")
    RETURN_NAMES = ("output", "denoised_output")

    FUNCTION = "sample"

    CATEGORY = "sampling/custom_sampling"

    def sample(self, model, add_noise, noise_seed, cfg, positive, negative, sampler, sigmas, latent_image):
        latent = latent_image
        latent_image = latent["samples"]
        if not add_noise:
            noise = torch.zeros(latent_image.size(), dtype=latent_image.dtype, layout=latent_image.layout, device="cpu")
        else:
            batch_inds = latent["batch_index"] if "batch_index" in latent else None
            noise = comfy.sample.prepare_noise(latent_image, noise_seed, batch_inds)

        noise_mask = None
        if "noise_mask" in latent:
            noise_mask = latent["noise_mask"]

        x0_output = {}
        callback = latent_preview.prepare_callback(model, sigmas.shape[-1] - 1, x0_output)

        disable_pbar = not comfy.utils.PROGRESS_BAR_ENABLED
        samples = comfy.sample.sample_custom(model, noise, cfg, sampler, sigmas, positive, negative, latent_image, noise_mask=noise_mask, callback=callback, disable_pbar=disable_pbar, seed=noise_seed)

        out = latent.copy()
        out["samples"] = samples
        if "x0" in x0_output:
            out_denoised = latent.copy()
            out_denoised["samples"] = model.model.process_latent_out(x0_output["x0"].cpu())
        else:
            out_denoised = out
        return (out, out_denoised)

```
