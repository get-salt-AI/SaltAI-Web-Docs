# RegionalSampler
## Documentation
- Class name: `RegionalSampler`
- Category: `ImpactPack/Regional`
- Output node: `False`

The RegionalSampler node is designed for advanced sampling techniques that allow for region-specific conditioning and sampling strategies. It enables the use of different samplers and modes for various regions within an image, providing a high degree of control over the generation process.
## Input types
### Required
- **`seed`**
    - unknown
    - Python dtype: `unknown`
    - Comfy dtype: `INT`
- **`seed_2nd`**
    - unknown
    - Python dtype: `unknown`
    - Comfy dtype: `INT`
- **`seed_2nd_mode`**
    - unknown
    - Python dtype: `unknown`
    - Comfy dtype: `['ignore', 'fixed', 'seed+seed_2nd'...]`
- **`steps`**
    - Specifies the number of steps to perform during the sampling process, affecting the detail and quality of the generated image.
    - Python dtype: `int`
    - Comfy dtype: `INT`
- **`base_only_steps`**
    - unknown
    - Python dtype: `unknown`
    - Comfy dtype: `INT`
- **`denoise`**
    - unknown
    - Python dtype: `unknown`
    - Comfy dtype: `FLOAT`
- **`samples`**
    - unknown
    - Python dtype: `unknown`
    - Comfy dtype: `LATENT`
- **`base_sampler`**
    - The primary sampler used for the initial stages of the sampling process, setting the foundation for further refinement.
    - Python dtype: `str`
    - Comfy dtype: `KSAMPLER_ADVANCED`
- **`regional_prompts`**
    - Region-specific prompts that guide the sampling process, allowing for targeted image manipulation.
    - Python dtype: `Dict[str, str]`
    - Comfy dtype: `REGIONAL_PROMPTS`
- **`overlap_factor`**
    - Controls the overlap between regions, affecting how seamlessly different regions blend together.
    - Python dtype: `int`
    - Comfy dtype: `INT`
- **`restore_latent`**
    - Indicates whether the original latent state should be restored after sampling, preserving the initial image structure.
    - Python dtype: `bool`
    - Comfy dtype: `BOOLEAN`
- **`additional_mode`**
    - The mode of operation for additional sampling strategies, offering flexibility in how regions are processed.
    - Python dtype: `str`
    - Comfy dtype: `['DISABLE', 'ratio additional', 'ratio between']`
- **`additional_sampler`**
    - An optional secondary sampler for enhancing specific regions, providing an extra layer of customization.
    - Python dtype: `str`
    - Comfy dtype: `STRING`
- **`additional_sigma_ratio`**
    - Adjusts the intensity of the additional sampler's effect, fine-tuning the regional modifications.
    - Python dtype: `float`
    - Comfy dtype: `FLOAT`
## Output types
- **`latent`**
    - The final latent representation after regional sampling, ready for further processing or conversion to an image.
    - Python dtype: `torch.Tensor`
    - Comfy dtype: `LATENT`
## Usage tips
- Infra type: `GPU`
- Common nodes: unknown


## Source code
```python
class RegionalSampler:
    @classmethod
    def INPUT_TYPES(s):
        return {"required": {
                     "seed": ("INT", {"default": 0, "min": 0, "max": 0xffffffffffffffff}),
                     "seed_2nd": ("INT", {"default": 0, "min": 0, "max": 0xffffffffffffffff}),
                     "seed_2nd_mode": (["ignore", "fixed", "seed+seed_2nd", "seed-seed_2nd", "increment", "decrement", "randomize"], ),
                     "steps": ("INT", {"default": 20, "min": 1, "max": 10000}),
                     "base_only_steps": ("INT", {"default": 2, "min": 0, "max": 10000}),
                     "denoise": ("FLOAT", {"default": 1.0, "min": 0.0, "max": 1.0, "step": 0.01}),
                     "samples": ("LATENT", ),
                     "base_sampler": ("KSAMPLER_ADVANCED", ),
                     "regional_prompts": ("REGIONAL_PROMPTS", ),
                     "overlap_factor": ("INT", {"default": 10, "min": 0, "max": 10000}),
                     "restore_latent": ("BOOLEAN", {"default": True, "label_on": "enabled", "label_off": "disabled"}),
                     "additional_mode": (["DISABLE", "ratio additional", "ratio between"], {"default": "ratio between"}),
                     "additional_sampler": (["AUTO", "euler", "heun", "heunpp2", "dpm_2", "dpm_fast", "dpmpp_2m", "ddpm"],),
                     "additional_sigma_ratio": ("FLOAT", {"default": 0.3, "min": 0.0, "max": 1.0, "step": 0.01}),
                     },
                 "hidden": {"unique_id": "UNIQUE_ID"},
                }

    RETURN_TYPES = ("LATENT", )
    FUNCTION = "doit"

    CATEGORY = "ImpactPack/Regional"

    @staticmethod
    def mask_erosion(samples, mask, grow_mask_by):
        mask = mask.clone()

        w = samples['samples'].shape[3]
        h = samples['samples'].shape[2]

        mask2 = torch.nn.functional.interpolate(mask.reshape((-1, 1, mask.shape[-2], mask.shape[-1])), size=(w, h), mode="bilinear")
        if grow_mask_by == 0:
            mask_erosion = mask2
        else:
            kernel_tensor = torch.ones((1, 1, grow_mask_by, grow_mask_by))
            padding = math.ceil((grow_mask_by - 1) / 2)

            mask_erosion = torch.clamp(torch.nn.functional.conv2d(mask2.round(), kernel_tensor, padding=padding), 0, 1)

        return mask_erosion[:, :, :w, :h].round()

    def doit(self, seed, seed_2nd, seed_2nd_mode, steps, base_only_steps, denoise, samples, base_sampler, regional_prompts, overlap_factor, restore_latent,
             additional_mode, additional_sampler, additional_sigma_ratio, unique_id=None):
        if restore_latent:
            latent_compositor = nodes.NODE_CLASS_MAPPINGS['LatentCompositeMasked']()
        else:
            latent_compositor = None

        masks = [regional_prompt.mask.numpy() for regional_prompt in regional_prompts]
        masks = [np.ceil(mask).astype(np.int32) for mask in masks]
        combined_mask = torch.from_numpy(np.bitwise_or.reduce(masks))

        inv_mask = torch.where(combined_mask == 0, torch.tensor(1.0), torch.tensor(0.0))

        adv_steps = int(steps / denoise)
        start_at_step = adv_steps - steps

        region_len = len(regional_prompts)
        total = steps*region_len

        leftover_noise = False
        if base_only_steps > 0:
            if seed_2nd_mode == 'ignore':
                leftover_noise = True

            samples = base_sampler.sample_advanced(True, seed, adv_steps, samples, start_at_step, start_at_step + base_only_steps, leftover_noise, recovery_mode="DISABLE")

        if seed_2nd_mode == "seed+seed_2nd":
            seed += seed_2nd
            if seed > 1125899906842624:
                seed = seed - 1125899906842624
        elif seed_2nd_mode == "seed-seed_2nd":
            seed -= seed_2nd
            if seed < 0:
                seed += 1125899906842624
        elif seed_2nd_mode != 'ignore':
            seed = seed_2nd

        new_latent_image = samples.copy()
        base_latent_image = None

        if not leftover_noise:
            add_noise = True
        else:
            add_noise = False

        for i in range(start_at_step+base_only_steps, adv_steps):
            core.update_node_status(unique_id, f"{i}/{steps} steps  |         ", ((i-start_at_step)*region_len)/total)

            new_latent_image['noise_mask'] = inv_mask
            new_latent_image = base_sampler.sample_advanced(add_noise, seed, adv_steps, new_latent_image, i, i + 1, True,
                                                            recovery_mode=additional_mode, recovery_sampler=additional_sampler, recovery_sigma_ratio=additional_sigma_ratio)

            if restore_latent:
                if 'noise_mask' in new_latent_image:
                    del new_latent_image['noise_mask']
                base_latent_image = new_latent_image.copy()

            j = 1
            for regional_prompt in regional_prompts:
                if restore_latent:
                    new_latent_image = base_latent_image.copy()

                core.update_node_status(unique_id, f"{i}/{steps} steps  |  {j}/{region_len}", ((i-start_at_step)*region_len + j)/total)

                region_mask = regional_prompt.get_mask_erosion(overlap_factor).squeeze(0).squeeze(0)

                new_latent_image['noise_mask'] = region_mask
                new_latent_image = regional_prompt.sampler.sample_advanced(False, seed, adv_steps, new_latent_image, i, i + 1, True,
                                                                           recovery_mode=additional_mode, recovery_sampler=additional_sampler, recovery_sigma_ratio=additional_sigma_ratio)

                if restore_latent:
                    del new_latent_image['noise_mask']
                    base_latent_image = latent_compositor.composite(base_latent_image, new_latent_image, 0, 0, False, region_mask)[0]
                    new_latent_image = base_latent_image

                j += 1

            add_noise = False

        # finalize
        core.update_node_status(unique_id, f"finalize")
        if base_latent_image is not None:
            new_latent_image = base_latent_image
        else:
            base_latent_image = new_latent_image

        new_latent_image['noise_mask'] = inv_mask
        new_latent_image = base_sampler.sample_advanced(False, seed, adv_steps, new_latent_image, adv_steps, adv_steps+1, False,
                                                        recovery_mode=additional_mode, recovery_sampler=additional_sampler, recovery_sigma_ratio=additional_sigma_ratio)

        core.update_node_status(unique_id, f"{steps}/{steps} steps", total)
        core.update_node_status(unique_id, "", None)

        if restore_latent:
            new_latent_image = base_latent_image

        if 'noise_mask' in new_latent_image:
            del new_latent_image['noise_mask']

        return (new_latent_image, )

```
