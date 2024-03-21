# TwoAdvancedSamplersForMask
## Documentation
- Class name: `TwoAdvancedSamplersForMask`
- Category: `ImpactPack/Sampler`
- Output node: `False`

This node applies advanced sampling techniques to a given latent image using two different samplers based on a mask. It first inverts the mask to apply the base sampler, then uses the mask erosion technique to refine the mask before applying the mask sampler. This process is iterated over a specified number of steps, allowing for detailed and controlled modifications of the latent image.
## Input types
### Required
- **`seed`**
    - The seed parameter is used to ensure reproducibility of the sampling process. It initializes the random number generator to produce the same results for the same input values.
    - Python dtype: `int`
    - Comfy dtype: `INT`
- **`steps`**
    - Defines the total number of sampling iterations to be performed. This controls the depth of the sampling process, affecting the final image quality and detail.
    - Python dtype: `int`
    - Comfy dtype: `INT`
- **`denoise`**
    - A factor that determines the intensity of noise reduction applied during the sampling process. It influences the smoothness and clarity of the final image.
    - Python dtype: `float`
    - Comfy dtype: `FLOAT`
- **`samples`**
    - The initial latent image to be modified through the sampling process. It serves as the starting point for the iterative application of the samplers.
    - Python dtype: `Dict[str, torch.Tensor]`
    - Comfy dtype: `LATENT`
- **`base_sampler`**
    - The primary sampler used to modify the latent image. It is applied to the inverted mask regions of the image.
    - Python dtype: `object`
    - Comfy dtype: `KSAMPLER_ADVANCED`
- **`mask_sampler`**
    - A secondary sampler specifically applied to the regions defined by the mask. It is used after the mask erosion process to refine these areas.
    - Python dtype: `object`
    - Comfy dtype: `KSAMPLER_ADVANCED`
- **`mask`**
    - Defines the regions of the latent image to be modified by the mask_sampler. It is used to selectively apply changes to specific areas of the image.
    - Python dtype: `torch.Tensor`
    - Comfy dtype: `MASK`
- **`overlap_factor`**
    - Determines the degree of mask erosion, which affects how the mask is expanded or contracted before being applied. This parameter allows for fine-tuning of the mask's influence on the sampling process.
    - Python dtype: `int`
    - Comfy dtype: `INT`
## Output types
- **`latent`**
    - The modified latent image resulting from the iterative application of the base and mask samplers, following the mask inversion and erosion processes.
    - Python dtype: `Dict[str, torch.Tensor]`
    - Comfy dtype: `LATENT`
## Usage tips
- Infra type: `GPU`
- Common nodes: unknown


## Source code
```python
class TwoAdvancedSamplersForMask:
    @classmethod
    def INPUT_TYPES(s):
        return {"required": {
                     "seed": ("INT", {"default": 0, "min": 0, "max": 0xffffffffffffffff}),
                     "steps": ("INT", {"default": 20, "min": 1, "max": 10000}),
                     "denoise": ("FLOAT", {"default": 1.0, "min": 0.0, "max": 1.0, "step": 0.01}),
                     "samples": ("LATENT", ),
                     "base_sampler": ("KSAMPLER_ADVANCED", ),
                     "mask_sampler": ("KSAMPLER_ADVANCED", ),
                     "mask": ("MASK", ),
                     "overlap_factor": ("INT", {"default": 10, "min": 0, "max": 10000})
                     },
                }

    RETURN_TYPES = ("LATENT", )
    FUNCTION = "doit"

    CATEGORY = "ImpactPack/Sampler"

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

    def doit(self, seed, steps, denoise, samples, base_sampler, mask_sampler, mask, overlap_factor):

        inv_mask = torch.where(mask != 1.0, torch.tensor(1.0), torch.tensor(0.0))

        adv_steps = int(steps / denoise)
        start_at_step = adv_steps - steps

        new_latent_image = samples.copy()

        mask_erosion = TwoAdvancedSamplersForMask.mask_erosion(samples, mask, overlap_factor)

        for i in range(start_at_step, adv_steps):
            add_noise = "enable" if i == start_at_step else "disable"
            return_with_leftover_noise = "enable" if i+1 != adv_steps else "disable"

            new_latent_image['noise_mask'] = inv_mask
            new_latent_image = base_sampler.sample_advanced(add_noise, seed, adv_steps, new_latent_image, i, i + 1, "enable", recovery_mode="ratio additional")

            new_latent_image['noise_mask'] = mask_erosion
            new_latent_image = mask_sampler.sample_advanced("disable", seed, adv_steps, new_latent_image, i, i + 1, return_with_leftover_noise, recovery_mode="ratio additional")

        del new_latent_image['noise_mask']

        return (new_latent_image, )

```
