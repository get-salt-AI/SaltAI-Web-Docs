# TwoSamplersForMask Upscaler Provider (pipe)
## Documentation
- Class name: `TwoSamplersForMaskUpscalerProviderPipe`
- Category: `ImpactPack/Upscale`
- Output node: `False`

This node is designed to provide a pipeline for upscaling images using two different samplers specifically for masks. It likely orchestrates the process of applying one sampler to the image, followed by the application of a second sampler, tailored to work with masks, to further refine the upscaled image.
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
    - The primary sampler used in the upscaling process. It plays a fundamental role in the initial phase of image upscaling.
    - Python dtype: `KSAMPLER`
    - Comfy dtype: `KSAMPLER`
- **`mask_sampler`**
    - A specialized sampler designed to work with masks. It is used after the base_sampler to refine the upscaling process by focusing on masked areas.
    - Python dtype: `KSAMPLER`
    - Comfy dtype: `KSAMPLER`
- **`mask`**
    - The mask that defines areas of interest for the mask_sampler to focus on during the upscaling process.
    - Python dtype: `torch.Tensor`
    - Comfy dtype: `MASK`
- **`basic_pipe`**
    - unknown
    - Python dtype: `unknown`
    - Comfy dtype: `BASIC_PIPE`
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
    - The upscaled latent image after being processed by both the base_sampler and the mask_sampler.
    - Python dtype: `torch.Tensor`
    - Comfy dtype: `UPSCALER`
## Usage tips
- Infra type: `GPU`
- Common nodes: unknown


## Source code
```python
class TwoSamplersForMaskUpscalerProviderPipe:
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
                     "basic_pipe": ("BASIC_PIPE",),
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

    def doit(self, scale_method, full_sample_schedule, use_tiled_vae, base_sampler, mask_sampler, mask, basic_pipe,
             full_sampler_opt=None, upscale_model_opt=None,
             pk_hook_base_opt=None, pk_hook_mask_opt=None, pk_hook_full_opt=None, tile_size=512):

        mask = make_2d_mask(mask)

        _, _, vae, _, _ = basic_pipe
        upscaler = core.TwoSamplersForMaskUpscaler(scale_method, full_sample_schedule, use_tiled_vae,
                                                   base_sampler, mask_sampler, mask, vae, full_sampler_opt, upscale_model_opt,
                                                   pk_hook_base_opt, pk_hook_mask_opt, pk_hook_full_opt, tile_size=tile_size)
        return (upscaler, )

```
