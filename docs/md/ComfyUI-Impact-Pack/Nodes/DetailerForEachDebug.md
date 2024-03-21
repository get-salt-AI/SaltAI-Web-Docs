# DetailerDebug (SEGS)
## Documentation
- Class name: `DetailerForEachDebug`
- Category: `ImpactPack/Detailer`
- Output node: `False`

The `DetailerForEachDebug` node is a specialized version of the `DetailerForEach` node designed for debugging purposes. It likely includes additional logging or diagnostic features to help understand the behavior of the detailer operations in a more granular way. This node is useful for developers or users who need to troubleshoot or optimize the detailer process.
## Input types
### Required
- **`image`**
    - unknown
    - Python dtype: `unknown`
    - Comfy dtype: `IMAGE`
- **`segs`**
    - unknown
    - Python dtype: `unknown`
    - Comfy dtype: `SEGS`
- **`model`**
    - unknown
    - Python dtype: `unknown`
    - Comfy dtype: `MODEL`
- **`clip`**
    - unknown
    - Python dtype: `unknown`
    - Comfy dtype: `CLIP`
- **`vae`**
    - unknown
    - Python dtype: `unknown`
    - Comfy dtype: `VAE`
- **`guide_size`**
    - unknown
    - Python dtype: `unknown`
    - Comfy dtype: `FLOAT`
- **`guide_size_for`**
    - unknown
    - Python dtype: `unknown`
    - Comfy dtype: `BOOLEAN`
- **`max_size`**
    - unknown
    - Python dtype: `unknown`
    - Comfy dtype: `FLOAT`
- **`seed`**
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
- **`denoise`**
    - unknown
    - Python dtype: `unknown`
    - Comfy dtype: `FLOAT`
- **`feather`**
    - unknown
    - Python dtype: `unknown`
    - Comfy dtype: `INT`
- **`noise_mask`**
    - unknown
    - Python dtype: `unknown`
    - Comfy dtype: `BOOLEAN`
- **`force_inpaint`**
    - unknown
    - Python dtype: `unknown`
    - Comfy dtype: `BOOLEAN`
- **`wildcard`**
    - unknown
    - Python dtype: `unknown`
    - Comfy dtype: `STRING`
- **`cycle`**
    - unknown
    - Python dtype: `unknown`
    - Comfy dtype: `INT`
### Optional
- **`detailer_hook`**
    - unknown
    - Python dtype: `unknown`
    - Comfy dtype: `DETAILER_HOOK`
- **`inpaint_model`**
    - unknown
    - Python dtype: `unknown`
    - Comfy dtype: `BOOLEAN`
- **`noise_mask_feather`**
    - unknown
    - Python dtype: `unknown`
    - Comfy dtype: `INT`
## Output types
- **`image`**
    - unknown
    - Python dtype: `unknown`
    - Comfy dtype: `IMAGE`
## Usage tips
- Infra type: `CPU`
- Common nodes: `PreviewImage,DetailerForEachDebug`


## Source code
```python
class DetailerForEachTest(DetailerForEach):
    RETURN_TYPES = ("IMAGE", "IMAGE", "IMAGE", "IMAGE", "IMAGE")
    RETURN_NAMES = ("image", "cropped", "cropped_refined", "cropped_refined_alpha", "cnet_images")
    OUTPUT_IS_LIST = (False, True, True, True, True)

    FUNCTION = "doit"

    CATEGORY = "ImpactPack/Detailer"

    def doit(self, image, segs, model, clip, vae, guide_size, guide_size_for, max_size, seed, steps, cfg, sampler_name,
             scheduler, positive, negative, denoise, feather, noise_mask, force_inpaint, wildcard, detailer_hook=None,
             cycle=1, inpaint_model=False, noise_mask_feather=0):

        if len(image) > 1:
            raise Exception('[Impact Pack] ERROR: DetailerForEach does not allow image batches.\nPlease refer to https://github.com/ltdrdata/ComfyUI-extension-tutorials/blob/Main/ComfyUI-Impact-Pack/tutorial/batching-detailer.md for more information.')

        enhanced_img, cropped, cropped_enhanced, cropped_enhanced_alpha, cnet_pil_list, new_segs = \
            DetailerForEach.do_detail(image, segs, model, clip, vae, guide_size, guide_size_for, max_size, seed, steps,
                                      cfg, sampler_name, scheduler, positive, negative, denoise, feather, noise_mask,
                                      force_inpaint, wildcard, detailer_hook,
                                      cycle=cycle, inpaint_model=inpaint_model, noise_mask_feather=noise_mask_feather)

        # set fallback image
        if len(cropped) == 0:
            cropped = [empty_pil_tensor()]

        if len(cropped_enhanced) == 0:
            cropped_enhanced = [empty_pil_tensor()]

        if len(cropped_enhanced_alpha) == 0:
            cropped_enhanced_alpha = [empty_pil_tensor()]

        if len(cnet_pil_list) == 0:
            cnet_pil_list = [empty_pil_tensor()]

        return enhanced_img, cropped, cropped_enhanced, cropped_enhanced_alpha, cnet_pil_list

```
