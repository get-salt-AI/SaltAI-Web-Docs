# DetailerDebug (SEGS/pipe)
## Documentation
- Class name: `DetailerForEachDebugPipe`
- Category: `ImpactPack/Detailer`
- Output node: `False`

This node is designed for debugging purposes within the Detailer pipeline, allowing for the inspection and manipulation of data at various stages of the pipeline to ensure correct operation and to identify any issues.
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
- **`basic_pipe`**
    - unknown
    - Python dtype: `unknown`
    - Comfy dtype: `BASIC_PIPE`
- **`wildcard`**
    - A flexible input that allows for dynamic data insertion into the pipeline for testing and debugging purposes. Its multiline and dynamic prompt capabilities enable complex debugging scenarios.
    - Python dtype: `str`
    - Comfy dtype: `STRING`
- **`refiner_ratio`**
    - unknown
    - Python dtype: `unknown`
    - Comfy dtype: `FLOAT`
- **`cycle`**
    - unknown
    - Python dtype: `unknown`
    - Comfy dtype: `INT`
### Optional
- **`detailer_hook`**
    - unknown
    - Python dtype: `unknown`
    - Comfy dtype: `DETAILER_HOOK`
- **`refiner_basic_pipe_opt`**
    - unknown
    - Python dtype: `unknown`
    - Comfy dtype: `BASIC_PIPE`
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
- **`segs`**
    - unknown
    - Python dtype: `unknown`
    - Comfy dtype: `SEGS`
- **`basic_pipe`**
    - unknown
    - Python dtype: `unknown`
    - Comfy dtype: `BASIC_PIPE`
## Usage tips
- Infra type: `CPU`
- Common nodes: `PreviewImage,Reroute,UltimateSDUpscale,FromBasicPipe`


## Source code
```python
class DetailerForEachTestPipe(DetailerForEachPipe):
    RETURN_TYPES = ("IMAGE", "SEGS", "BASIC_PIPE", "IMAGE", "IMAGE", "IMAGE", "IMAGE", )
    RETURN_NAMES = ("image", "segs", "basic_pipe", "cropped", "cropped_refined", "cropped_refined_alpha", 'cnet_images')
    OUTPUT_IS_LIST = (False, False, False, True, True, True, True)

    FUNCTION = "doit"

    CATEGORY = "ImpactPack/Detailer"

    def doit(self, image, segs, guide_size, guide_size_for, max_size, seed, steps, cfg, sampler_name, scheduler,
             denoise, feather, noise_mask, force_inpaint, basic_pipe, wildcard, cycle=1,
             refiner_ratio=None, detailer_hook=None, refiner_basic_pipe_opt=None, inpaint_model=False, noise_mask_feather=0):

        if len(image) > 1:
            raise Exception('[Impact Pack] ERROR: DetailerForEach does not allow image batches.\nPlease refer to https://github.com/ltdrdata/ComfyUI-extension-tutorials/blob/Main/ComfyUI-Impact-Pack/tutorial/batching-detailer.md for more information.')

        model, clip, vae, positive, negative = basic_pipe

        if refiner_basic_pipe_opt is None:
            refiner_model, refiner_clip, refiner_positive, refiner_negative = None, None, None, None
        else:
            refiner_model, refiner_clip, _, refiner_positive, refiner_negative = refiner_basic_pipe_opt

        enhanced_img, cropped, cropped_enhanced, cropped_enhanced_alpha, cnet_pil_list, new_segs = \
            DetailerForEach.do_detail(image, segs, model, clip, vae, guide_size, guide_size_for, max_size, seed, steps, cfg,
                                      sampler_name, scheduler, positive, negative, denoise, feather, noise_mask,
                                      force_inpaint, wildcard, detailer_hook,
                                      refiner_ratio=refiner_ratio, refiner_model=refiner_model,
                                      refiner_clip=refiner_clip, refiner_positive=refiner_positive,
                                      refiner_negative=refiner_negative,
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

        return enhanced_img, new_segs, basic_pipe, cropped, cropped_enhanced, cropped_enhanced_alpha, cnet_pil_list

```
