# TwoSamplersForMask Upscaler Provider
## Documentation
- Class name: `TwoSamplersForMaskUpscalerProvider`
- Category: `ImpactPack/Upscale`
- Output node: `False`

This node provides a mechanism to upscale an image using two different samplers based on a mask. It utilizes one sampler for the areas specified by the mask and another sampler for the rest of the image. This approach allows for differentiated processing, enhancing specific regions of an image while treating the rest differently.
## Input types
### Required
- **`scale_method`**
    - unknown
    - Python dtype: `unknown`
    - Comfy dtype: `['nearest-exact', 'bilinear', 'lanczos'...]`
- **`full_sample_schedule`**
    - unknown
    - Python dtype: `unknown`
    - Comfy dtype: `['none', 'interleave1', 'interleave2'...]`
- **`use_tiled_vae`**
    - unknown
    - Python dtype: `unknown`
    - Comfy dtype: `BOOLEAN`
- **`base_sampler`**
    - The sampler used for upscaling the areas of the image not specified by the mask. It defines the upscaling behavior for the majority of the image.
    - Python dtype: `KSAMPLER`
    - Comfy dtype: `KSAMPLER`
- **`mask_sampler`**
    - The sampler specifically used for the areas of the image defined by the mask. It allows for targeted upscaling and processing of these regions.
    - Python dtype: `KSAMPLER`
    - Comfy dtype: `KSAMPLER`
- **`mask`**
    - A binary mask defining areas of the image to be processed differently. It guides the application of the two samplers.
    - Python dtype: `torch.Tensor`
    - Comfy dtype: `MASK`
- **`vae`**
    - unknown
    - Python dtype: `unknown`
    - Comfy dtype: `VAE`
- **`tile_size`**
    - unknown
    - Python dtype: `unknown`
    - Comfy dtype: `INT`
### Optional
- **`full_sampler_opt`**
    - unknown
    - Python dtype: `unknown`
    - Comfy dtype: `KSAMPLER`
- **`upscale_model_opt`**
    - unknown
    - Python dtype: `unknown`
    - Comfy dtype: `UPSCALE_MODEL`
- **`pk_hook_base_opt`**
    - unknown
    - Python dtype: `unknown`
    - Comfy dtype: `PK_HOOK`
- **`pk_hook_mask_opt`**
    - unknown
    - Python dtype: `unknown`
    - Comfy dtype: `PK_HOOK`
- **`pk_hook_full_opt`**
    - unknown
    - Python dtype: `unknown`
    - Comfy dtype: `PK_HOOK`
## Output types
- **`upscaler`**
    - unknown
    - Python dtype: `unknown`
    - Comfy dtype: `UPSCALER`
## Usage tips
- Infra type: `GPU`
- Common nodes: unknown


## Source code
```python
class TwoSamplersForMaskUpscalerProvider:
    upscale_methods = ["nearest-exact", "bilinear", "lanczos", "area"]

    @classmethod
    def INPUT_TYPES(s):
        return {"required": {
                     "scale_method": (s.upscale_methods,),
                     "full_sample_schedule": (
                         ["none", "interleave1", "interleave2", "interleave3",
                          "last1", "last2",
                          "interleave1+last1", "interleave2+last1", "interleave3+last1",
                          ],),
                     "use_tiled_vae": ("BOOLEAN", {"default": False, "label_on": "enabled", "label_off": "disabled"}),
                     "base_sampler": ("KSAMPLER", ),
                     "mask_sampler": ("KSAMPLER", ),
                     "mask": ("MASK", ),
                     "vae": ("VAE",),
                     "tile_size": ("INT", {"default": 512, "min": 320, "max": 4096, "step": 64}),
                     },
                "optional": {
                        "full_sampler_opt": ("KSAMPLER",),
                        "upscale_model_opt": ("UPSCALE_MODEL", ),
                        "pk_hook_base_opt": ("PK_HOOK", ),
                        "pk_hook_mask_opt": ("PK_HOOK", ),
                        "pk_hook_full_opt": ("PK_HOOK", ),
                    }
                }

    RETURN_TYPES = ("UPSCALER", )
    FUNCTION = "doit"

    CATEGORY = "ImpactPack/Upscale"

    def doit(self, scale_method, full_sample_schedule, use_tiled_vae, base_sampler, mask_sampler, mask, vae,
             full_sampler_opt=None, upscale_model_opt=None,
             pk_hook_base_opt=None, pk_hook_mask_opt=None, pk_hook_full_opt=None, tile_size=512):
        upscaler = core.TwoSamplersForMaskUpscaler(scale_method, full_sample_schedule, use_tiled_vae,
                                                   base_sampler, mask_sampler, mask, vae, full_sampler_opt, upscale_model_opt,
                                                   pk_hook_base_opt, pk_hook_mask_opt, pk_hook_full_opt, tile_size=tile_size)
        return (upscaler, )

```
