# Detailer (SEGS/pipe)
## Documentation
- Class name: `DetailerForEachPipe`
- Category: `ImpactPack/Detailer`
- Output node: `False`

Given the context, it appears there was a misunderstanding in the original response. The node 'DetailerForEachPipe' does not exist within the provided context, which includes various classes related to the manipulation and transformation of detailer pipes in a pipeline architecture. The classes provided offer functionalities such as converting, editing, and extracting components from detailer and basic pipes, but there is no specific mention or functionality described as 'DetailerForEachPipe'. Therefore, it's not possible to provide a detailed explanation of functionality, input types, output types, or infra type for a node that is not defined.
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
    - unknown
    - Python dtype: `unknown`
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
- Common nodes: unknown


## Source code
```python
class DetailerForEachPipe:
    @classmethod
    def INPUT_TYPES(s):
        return {"required": {
                      "image": ("IMAGE", ),
                      "segs": ("SEGS", ),
                      "guide_size": ("FLOAT", {"default": 384, "min": 64, "max": nodes.MAX_RESOLUTION, "step": 8}),
                      "guide_size_for": ("BOOLEAN", {"default": True, "label_on": "bbox", "label_off": "crop_region"}),
                      "max_size": ("FLOAT", {"default": 1024, "min": 64, "max": nodes.MAX_RESOLUTION, "step": 8}),
                      "seed": ("INT", {"default": 0, "min": 0, "max": 0xffffffffffffffff}),
                      "steps": ("INT", {"default": 20, "min": 1, "max": 10000}),
                      "cfg": ("FLOAT", {"default": 8.0, "min": 0.0, "max": 100.0}),
                      "sampler_name": (comfy.samplers.KSampler.SAMPLERS,),
                      "scheduler": (comfy.samplers.KSampler.SCHEDULERS,),
                      "denoise": ("FLOAT", {"default": 0.5, "min": 0.0001, "max": 1.0, "step": 0.01}),
                      "feather": ("INT", {"default": 5, "min": 0, "max": 100, "step": 1}),
                      "noise_mask": ("BOOLEAN", {"default": True, "label_on": "enabled", "label_off": "disabled"}),
                      "force_inpaint": ("BOOLEAN", {"default": True, "label_on": "enabled", "label_off": "disabled"}),
                      "basic_pipe": ("BASIC_PIPE", ),
                      "wildcard": ("STRING", {"multiline": True, "dynamicPrompts": False}),
                      "refiner_ratio": ("FLOAT", {"default": 0.2, "min": 0.0, "max": 1.0}),

                      "cycle": ("INT", {"default": 1, "min": 1, "max": 10, "step": 1}),
                     },
                "optional": {
                     "detailer_hook": ("DETAILER_HOOK",),
                     "refiner_basic_pipe_opt": ("BASIC_PIPE",),
                     "inpaint_model": ("BOOLEAN", {"default": False, "label_on": "enabled", "label_off": "disabled"}),
                     "noise_mask_feather": ("INT", {"default": 0, "min": 0, "max": 100, "step": 1}),
                    }
                }

    RETURN_TYPES = ("IMAGE", "SEGS", "BASIC_PIPE", "IMAGE")
    RETURN_NAMES = ("image", "segs", "basic_pipe", "cnet_images")
    OUTPUT_IS_LIST = (False, False, False, True)
    FUNCTION = "doit"

    CATEGORY = "ImpactPack/Detailer"

    def doit(self, image, segs, guide_size, guide_size_for, max_size, seed, steps, cfg, sampler_name, scheduler,
             denoise, feather, noise_mask, force_inpaint, basic_pipe, wildcard,
             refiner_ratio=None, detailer_hook=None, refiner_basic_pipe_opt=None,
             cycle=1, inpaint_model=False, noise_mask_feather=0):

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
                                      refiner_clip=refiner_clip, refiner_positive=refiner_positive, refiner_negative=refiner_negative,
                                      cycle=cycle, inpaint_model=inpaint_model, noise_mask_feather=noise_mask_feather)

        # set fallback image
        if len(cnet_pil_list) == 0:
            cnet_pil_list = [empty_pil_tensor()]

        return (enhanced_img, new_segs, basic_pipe, cnet_pil_list)

```
