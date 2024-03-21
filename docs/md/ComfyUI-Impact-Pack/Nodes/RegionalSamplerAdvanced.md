# RegionalSamplerAdvanced
## Documentation
- Class name: `RegionalSamplerAdvanced`
- Category: `ImpactPack/Regional`
- Output node: `False`

This node is designed to perform advanced regional sampling, integrating various input parameters to generate or modify latent images based on regional prompts and additional sampling configurations. It combines noise addition, step-based processing, and conditional sampling to achieve specific visual effects or modifications in generated images.
## Input types
### Required
- **`add_noise`**
    - Determines whether noise should be added to the sampling process, affecting the visual texture and details of the generated image.
    - Python dtype: `bool`
    - Comfy dtype: `BOOLEAN`
- **`noise_seed`**
    - Specifies the seed for noise generation, ensuring reproducibility of the noise pattern in the sampling process.
    - Python dtype: `int`
    - Comfy dtype: `INT`
- **`steps`**
    - Defines the number of steps in the sampling process, impacting the detail and refinement of the generated image.
    - Python dtype: `int`
    - Comfy dtype: `INT`
- **`start_at_step`**
    - Indicates the starting step for the sampling process, allowing for control over the initial state of image generation.
    - Python dtype: `int`
    - Comfy dtype: `INT`
- **`end_at_step`**
    - Specifies the ending step for the sampling process, determining the final state of image refinement.
    - Python dtype: `int`
    - Comfy dtype: `INT`
- **`overlap_factor`**
    - Controls the degree of overlap between sampled regions, affecting the blending and coherence of the generated image.
    - Python dtype: `int`
    - Comfy dtype: `INT`
- **`restore_latent`**
    - Determines whether the original latent state should be restored after sampling, impacting the preservation of initial image characteristics.
    - Python dtype: `bool`
    - Comfy dtype: `BOOLEAN`
- **`return_with_leftover_noise`**
    - Decides if the sampling result should include leftover noise, influencing the final visual texture of the image.
    - Python dtype: `bool`
    - Comfy dtype: `BOOLEAN`
- **`latent_image`**
    - The latent image to be processed, serving as the base for the sampling operation.
    - Python dtype: `torch.Tensor`
    - Comfy dtype: `LATENT`
- **`base_sampler`**
    - The base sampler used for the initial stage of the sampling process, setting the foundation for further modifications.
    - Python dtype: `str`
    - Comfy dtype: `KSAMPLER_ADVANCED`
- **`regional_prompts`**
    - Regional prompts guiding the sampling process, directing the generation or modification of specific image regions.
    - Python dtype: `List[RegionalPrompt]`
    - Comfy dtype: `REGIONAL_PROMPTS`
- **`additional_mode`**
    - Specifies the mode for additional sampling, allowing for customization of the sampling strategy beyond the base configuration.
    - Python dtype: `str`
    - Comfy dtype: `STRING`
- **`additional_sampler`**
    - The additional sampler to be used in conjunction with the base sampler, enhancing the sampling capabilities.
    - Python dtype: `str`
    - Comfy dtype: `STRING`
- **`additional_sigma_ratio`**
    - Determines the ratio of sigma used in additional sampling, affecting the intensity and characteristics of the modifications.
    - Python dtype: `float`
    - Comfy dtype: `FLOAT`
## Output types
- **`latent`**
    - The resulting latent image after the advanced regional sampling process.
    - Python dtype: `torch.Tensor`
    - Comfy dtype: `LATENT`
## Usage tips
- Infra type: `GPU`
- Common nodes: unknown


## Source code
```python
class RegionalSamplerAdvanced:
    @classmethod
    def INPUT_TYPES(s):
        return {"required": {
                     "add_noise": ("BOOLEAN", {"default": True, "label_on": "enabled", "label_off": "disabled"}),
                     "noise_seed": ("INT", {"default": 0, "min": 0, "max": 0xffffffffffffffff}),
                     "steps": ("INT", {"default": 20, "min": 1, "max": 10000}),
                     "start_at_step": ("INT", {"default": 0, "min": 0, "max": 10000}),
                     "end_at_step": ("INT", {"default": 10000, "min": 0, "max": 10000}),
                     "overlap_factor": ("INT", {"default": 10, "min": 0, "max": 10000}),
                     "restore_latent": ("BOOLEAN", {"default": True, "label_on": "enabled", "label_off": "disabled"}),
                     "return_with_leftover_noise": ("BOOLEAN", {"default": False, "label_on": "enabled", "label_off": "disabled"}),
                     "latent_image": ("LATENT", ),
                     "base_sampler": ("KSAMPLER_ADVANCED", ),
                     "regional_prompts": ("REGIONAL_PROMPTS", ),
                     "additional_mode": (["DISABLE", "ratio additional", "ratio between"], {"default": "ratio between"}),
                     "additional_sampler": (["AUTO", "euler", "heun", "heunpp2", "dpm_2", "dpm_fast", "dpmpp_2m", "ddpm"],),
                     "additional_sigma_ratio": ("FLOAT", {"default": 0.3, "min": 0.0, "max": 1.0, "step": 0.01}),
                     },
                 "hidden": {"unique_id": "UNIQUE_ID"},
                }

    RETURN_TYPES = ("LATENT", )
    FUNCTION = "doit"

    CATEGORY = "ImpactPack/Regional"

    def doit(self, add_noise, noise_seed, steps, start_at_step, end_at_step, overlap_factor, restore_latent, return_with_leftover_noise, latent_image, base_sampler, regional_prompts,
             additional_mode, additional_sampler, additional_sigma_ratio, unique_id):

        if restore_latent:
            latent_compositor = nodes.NODE_CLASS_MAPPINGS['LatentCompositeMasked']()
        else:
            latent_compositor = None

        masks = [regional_prompt.mask.numpy() for regional_prompt in regional_prompts]
        masks = [np.ceil(mask).astype(np.int32) for mask in masks]
        combined_mask = torch.from_numpy(np.bitwise_or.reduce(masks))

        inv_mask = torch.where(combined_mask == 0, torch.tensor(1.0), torch.tensor(0.0))

        region_len = len(regional_prompts)
        end_at_step = min(steps, end_at_step)
        total = (end_at_step - start_at_step) * region_len

        new_latent_image = latent_image.copy()
        base_latent_image = None
        region_masks = {}

        for i in range(start_at_step, end_at_step-1):
            core.update_node_status(unique_id, f"{start_at_step+i}/{end_at_step} steps  |         ", ((i-start_at_step)*region_len)/total)

            cur_add_noise = True if i == start_at_step and add_noise else False

            new_latent_image['noise_mask'] = inv_mask
            new_latent_image = base_sampler.sample_advanced(cur_add_noise, noise_seed, steps, new_latent_image, i, i + 1, True,
                                                            recovery_mode=additional_mode, recovery_sampler=additional_sampler, recovery_sigma_ratio=additional_sigma_ratio)

            if restore_latent:
                del new_latent_image['noise_mask']
                base_latent_image = new_latent_image.copy()

            j = 1
            for regional_prompt in regional_prompts:
                if restore_latent:
                    new_latent_image = base_latent_image.copy()

                core.update_node_status(unique_id, f"{start_at_step+i}/{end_at_step} steps  |  {j}/{region_len}", ((i-start_at_step)*region_len + j)/total)

                if j not in region_masks:
                    region_mask = regional_prompt.get_mask_erosion(overlap_factor).squeeze(0).squeeze(0)
                    region_masks[j] = region_mask
                else:
                    region_mask = region_masks[j]

                new_latent_image['noise_mask'] = region_mask
                new_latent_image = regional_prompt.sampler.sample_advanced(False, noise_seed, steps, new_latent_image, i, i + 1, True,
                                                                           recovery_mode=additional_mode, recovery_sampler=additional_sampler, recovery_sigma_ratio=additional_sigma_ratio)

                if restore_latent:
                    del new_latent_image['noise_mask']
                    base_latent_image = latent_compositor.composite(base_latent_image, new_latent_image, 0, 0, False, region_mask)[0]
                    new_latent_image = base_latent_image

                j += 1

        # finalize
        core.update_node_status(unique_id, f"finalize")
        if base_latent_image is not None:
            new_latent_image = base_latent_image
        else:
            base_latent_image = new_latent_image

        new_latent_image['noise_mask'] = inv_mask
        new_latent_image = base_sampler.sample_advanced(False, noise_seed, steps, new_latent_image, end_at_step-1, end_at_step, return_with_leftover_noise,
                                                        recovery_mode=additional_mode, recovery_sampler=additional_sampler, recovery_sigma_ratio=additional_sigma_ratio)

        core.update_node_status(unique_id, f"{end_at_step}/{end_at_step} steps", total)
        core.update_node_status(unique_id, "", None)

        if restore_latent:
            new_latent_image = base_latent_image

        if 'noise_mask' in new_latent_image:
            del new_latent_image['noise_mask']

        return (new_latent_image, )

```
