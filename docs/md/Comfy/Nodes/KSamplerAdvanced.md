# KSampler (Advanced)
## Documentation
- Class name: `KSamplerAdvanced`
- Category: `sampling`
- Output node: `False`

The `KSamplerAdvanced` node appears to be a conceptual or placeholder entity, as there is no direct implementation or method details provided in the context. Typically, such a node might represent an advanced version of a sampling mechanism, potentially offering more sophisticated or customizable sampling strategies compared to its base version. However, without specific implementation details, it's not possible to provide a precise analysis of its functionality.
## Input types
### Required
- **`model`**
    - unknown
    - Python dtype: `unknown`
    - Comfy dtype: `MODEL`
- **`add_noise`**
    - unknown
    - Python dtype: `unknown`
    - Comfy dtype: `['enable', 'disable'...]`
- **`noise_seed`**
    - unknown
    - Python dtype: `unknown`
    - Comfy dtype: `INT`
- **`steps`**
    - unknown
    - Python dtype: `unknown`
    - Comfy dtype: `INT`
- **`cfg`**
    - unknown
    - Python dtype: `unknown`
    - Comfy dtype: `FLOAT`
- **`sampler_name`**
    - unknown
    - Python dtype: `unknown`
    - Comfy dtype: `['euler', 'euler_ancestral', 'heun'...]`
- **`scheduler`**
    - unknown
    - Python dtype: `unknown`
    - Comfy dtype: `['normal', 'karras', 'exponential'...]`
- **`positive`**
    - unknown
    - Python dtype: `unknown`
    - Comfy dtype: `CONDITIONING`
- **`negative`**
    - unknown
    - Python dtype: `unknown`
    - Comfy dtype: `CONDITIONING`
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
    - Comfy dtype: `['disable', 'enable'...]`
## Output types
- **`latent`**
    - unknown
    - Python dtype: `unknown`
    - Comfy dtype: `LATENT`
## Usage tips
- Infra type: `CPU`
- Common nodes: `VAEDecode,Reroute,workflow/KSampler + Vae,gcLatentTunnel,Anything Everywhere,LatentCompositeMasked,KSampler,LatentBlend,Mute / Bypass Repeater (rgthree)`


## Source code
```python
class KSamplerAdvanced:
    @classmethod
    def INPUT_TYPES(s):
        return {"required":
                    {"model": ("MODEL",),
                    "add_noise": (["enable", "disable"], ),
                    "noise_seed": ("INT", {"default": 0, "min": 0, "max": 0xffffffffffffffff}),
                    "steps": ("INT", {"default": 20, "min": 1, "max": 10000}),
                    "cfg": ("FLOAT", {"default": 8.0, "min": 0.0, "max": 100.0, "step":0.1, "round": 0.01}),
                    "sampler_name": (comfy.samplers.KSampler.SAMPLERS, ),
                    "scheduler": (comfy.samplers.KSampler.SCHEDULERS, ),
                    "positive": ("CONDITIONING", ),
                    "negative": ("CONDITIONING", ),
                    "latent_image": ("LATENT", ),
                    "start_at_step": ("INT", {"default": 0, "min": 0, "max": 10000}),
                    "end_at_step": ("INT", {"default": 10000, "min": 0, "max": 10000}),
                    "return_with_leftover_noise": (["disable", "enable"], ),
                     }
                }

    RETURN_TYPES = ("LATENT",)
    FUNCTION = "sample"

    CATEGORY = "sampling"

    def sample(self, model, add_noise, noise_seed, steps, cfg, sampler_name, scheduler, positive, negative, latent_image, start_at_step, end_at_step, return_with_leftover_noise, denoise=1.0):
        force_full_denoise = True
        if return_with_leftover_noise == "enable":
            force_full_denoise = False
        disable_noise = False
        if add_noise == "disable":
            disable_noise = True
        return common_ksampler(model, noise_seed, steps, cfg, sampler_name, scheduler, positive, negative, latent_image, denoise=denoise, disable_noise=disable_noise, start_step=start_at_step, last_step=end_at_step, force_full_denoise=force_full_denoise)

```
