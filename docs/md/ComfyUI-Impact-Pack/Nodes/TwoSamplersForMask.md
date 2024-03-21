# TwoSamplersForMask
## Documentation
- Class name: `TwoSamplersForMask`
- Category: `ImpactPack/Sampler`
- Output node: `False`

The `TwoSamplersForMask` node applies two different sampling processes to a latent image using a mask. Initially, it applies an inverse mask to the latent image and processes it with a base sampler. Then, it applies the original mask to the modified latent image and processes it with a mask sampler. This method allows for selective editing or enhancement of specific regions within an image.
## Input types
### Required
- **`latent_image`**
    - The latent representation of an image to be processed. It serves as the base for selective editing or enhancement through the application of masks and samplers.
    - Python dtype: `Dict[str, torch.Tensor]`
    - Comfy dtype: `LATENT`
- **`base_sampler`**
    - A sampler that processes the entire latent image except for the regions specified by the mask. It's used for the initial modification of the image.
    - Python dtype: `KSAMPLER`
    - Comfy dtype: `KSAMPLER`
- **`mask_sampler`**
    - A sampler that specifically targets and processes the regions of the latent image defined by the mask. It's used for further modification or enhancement of these regions.
    - Python dtype: `KSAMPLER`
    - Comfy dtype: `KSAMPLER`
- **`mask`**
    - A binary mask defining regions of interest within the latent image. Regions marked by the mask are processed differently, allowing for targeted modifications.
    - Python dtype: `torch.Tensor`
    - Comfy dtype: `MASK`
## Output types
- **`latent`**
    - The modified latent image after being processed by both the base and mask samplers. This image reflects the selective enhancements or edits made to the original.
    - Python dtype: `Dict[str, torch.Tensor]`
    - Comfy dtype: `LATENT`
## Usage tips
- Infra type: `GPU`
- Common nodes: unknown


## Source code
```python
class TwoSamplersForMask:
    @classmethod
    def INPUT_TYPES(s):
        return {"required": {
                     "latent_image": ("LATENT", ),
                     "base_sampler": ("KSAMPLER", ),
                     "mask_sampler": ("KSAMPLER", ),
                     "mask": ("MASK", )
                     },
                }

    RETURN_TYPES = ("LATENT", )
    FUNCTION = "doit"

    CATEGORY = "ImpactPack/Sampler"

    def doit(self, latent_image, base_sampler, mask_sampler, mask):
        inv_mask = torch.where(mask != 1.0, torch.tensor(1.0), torch.tensor(0.0))

        latent_image['noise_mask'] = inv_mask
        new_latent_image = base_sampler.sample(latent_image)

        new_latent_image['noise_mask'] = mask
        new_latent_image = mask_sampler.sample(new_latent_image)

        del new_latent_image['noise_mask']

        return (new_latent_image, )

```
