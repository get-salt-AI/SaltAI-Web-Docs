# MaskDetailer (pipe)
## Documentation
- Class name: `MaskDetailerPipe`
- Category: `ImpactPack/Detailer`
- Output node: `False`

The MaskDetailerPipe is designed to enhance or detail specific areas of an image, particularly focusing on masks. It operates within a pipeline to apply detailed modifications or enhancements to masked regions, improving the overall visual quality or adjusting specific aspects according to the given parameters.
## Input types
### Required
- **`image`**
    - unknown
    - Python dtype: `unknown`
    - Comfy dtype: `IMAGE`
- **`mask`**
    - unknown
    - Python dtype: `unknown`
    - Comfy dtype: `MASK`
- **`basic_pipe`**
    - unknown
    - Python dtype: `unknown`
    - Comfy dtype: `BASIC_PIPE`
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
- **`mask_mode`**
    - unknown
    - Python dtype: `unknown`
    - Comfy dtype: `BOOLEAN`
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
- **`crop_factor`**
    - unknown
    - Python dtype: `unknown`
    - Comfy dtype: `FLOAT`
- **`drop_size`**
    - unknown
    - Python dtype: `unknown`
    - Comfy dtype: `INT`
- **`refiner_ratio`**
    - unknown
    - Python dtype: `unknown`
    - Comfy dtype: `FLOAT`
- **`batch_size`**
    - unknown
    - Python dtype: `unknown`
    - Comfy dtype: `INT`
- **`cycle`**
    - unknown
    - Python dtype: `unknown`
    - Comfy dtype: `INT`
### Optional
- **`refiner_basic_pipe_opt`**
    - unknown
    - Python dtype: `unknown`
    - Comfy dtype: `BASIC_PIPE`
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
- **`basic_pipe`**
    - The refined pipeline configuration after detailing operations, including any refinements applied.
    - Python dtype: `Tuple[torch.nn.Module, ...]`
    - Comfy dtype: `BASIC_PIPE`
## Usage tips
- Infra type: `GPU`
- Common nodes: unknown


## Source code
```python
class MaskDetailerPipe:
    @classmethod
    def INPUT_TYPES(s):
        return {"required": {
                    "image": ("IMAGE", ),
                    "mask": ("MASK", ),
                    "basic_pipe": ("BASIC_PIPE",),

                    "guide_size": ("FLOAT", {"default": 384, "min": 64, "max": nodes.MAX_RESOLUTION, "step": 8}),
                    "guide_size_for": ("BOOLEAN", {"default": True, "label_on": "mask bbox", "label_off": "crop region"}),
                    "max_size": ("FLOAT", {"default": 1024, "min": 64, "max": nodes.MAX_RESOLUTION, "step": 8}),
                    "mask_mode": ("BOOLEAN", {"default": True, "label_on": "masked only", "label_off": "whole"}),

                    "seed": ("INT", {"default": 0, "min": 0, "max": 0xffffffffffffffff}),
                    "steps": ("INT", {"default": 20, "min": 1, "max": 10000}),
                    "cfg": ("FLOAT", {"default": 8.0, "min": 0.0, "max": 100.0}),
                    "sampler_name": (comfy.samplers.KSampler.SAMPLERS,),
                    "scheduler": (comfy.samplers.KSampler.SCHEDULERS,),
                    "denoise": ("FLOAT", {"default": 0.5, "min": 0.0001, "max": 1.0, "step": 0.01}),

                    "feather": ("INT", {"default": 5, "min": 0, "max": 100, "step": 1}),
                    "crop_factor": ("FLOAT", {"default": 3.0, "min": 1.0, "max": 10, "step": 0.1}),
                    "drop_size": ("INT", {"min": 1, "max": MAX_RESOLUTION, "step": 1, "default": 10}),
                    "refiner_ratio": ("FLOAT", {"default": 0.2, "min": 0.0, "max": 1.0}),
                    "batch_size": ("INT", {"default": 1, "min": 1, "max": 100}),

                    "cycle": ("INT", {"default": 1, "min": 1, "max": 10, "step": 1}),
                   },
                "optional": {
                    "refiner_basic_pipe_opt": ("BASIC_PIPE", ),
                    "detailer_hook": ("DETAILER_HOOK",),
                    "inpaint_model": ("BOOLEAN", {"default": False, "label_on": "enabled", "label_off": "disabled"}),
                    "noise_mask_feather": ("INT", {"default": 0, "min": 0, "max": 100, "step": 1}),
                   }
                }

    RETURN_TYPES = ("IMAGE", "IMAGE", "IMAGE", "BASIC_PIPE", "BASIC_PIPE")
    RETURN_NAMES = ("image", "cropped_refined", "cropped_enhanced_alpha", "basic_pipe", "refiner_basic_pipe_opt")
    OUTPUT_IS_LIST = (False, True, True, False, False)
    FUNCTION = "doit"

    CATEGORY = "ImpactPack/Detailer"

    def doit(self, image, mask, basic_pipe, guide_size, guide_size_for, max_size, mask_mode,
             seed, steps, cfg, sampler_name, scheduler, denoise,
             feather, crop_factor, drop_size, refiner_ratio, batch_size, cycle=1,
             refiner_basic_pipe_opt=None, detailer_hook=None, inpaint_model=False, noise_mask_feather=0):

        if len(image) > 1:
            raise Exception('[Impact Pack] ERROR: MaskDetailer does not allow image batches.\nPlease refer to https://github.com/ltdrdata/ComfyUI-extension-tutorials/blob/Main/ComfyUI-Impact-Pack/tutorial/batching-detailer.md for more information.')

        model, clip, vae, positive, negative = basic_pipe

        if refiner_basic_pipe_opt is None:
            refiner_model, refiner_clip, refiner_positive, refiner_negative = None, None, None, None
        else:
            refiner_model, refiner_clip, _, refiner_positive, refiner_negative = refiner_basic_pipe_opt

        # create segs
        if mask is not None:
            mask = make_2d_mask(mask)
            segs = core.mask_to_segs(mask, False, crop_factor, False, drop_size)
        else:
            segs = ((image.shape[1], image.shape[2]), [])

        enhanced_img_batch = None
        cropped_enhanced_list = []
        cropped_enhanced_alpha_list = []

        for i in range(batch_size):
            if mask is not None:
                enhanced_img, _, cropped_enhanced, cropped_enhanced_alpha, _, _ = \
                    DetailerForEach.do_detail(image, segs, model, clip, vae, guide_size, guide_size_for, max_size, seed+i, steps,
                                              cfg, sampler_name, scheduler, positive, negative, denoise, feather, mask_mode,
                                              force_inpaint=True, wildcard_opt=None, detailer_hook=detailer_hook,
                                              refiner_ratio=refiner_ratio, refiner_model=refiner_model, refiner_clip=refiner_clip,
                                              refiner_positive=refiner_positive, refiner_negative=refiner_negative,
                                              cycle=cycle, inpaint_model=inpaint_model, noise_mask_feather=noise_mask_feather)
            else:
                enhanced_img, cropped_enhanced, cropped_enhanced_alpha = image, [], []

            if enhanced_img_batch is None:
                enhanced_img_batch = enhanced_img
            else:
                enhanced_img_batch = torch.cat((enhanced_img_batch, enhanced_img), dim=0)

            cropped_enhanced_list += cropped_enhanced
            cropped_enhanced_alpha_list += cropped_enhanced_alpha

        # set fallback image
        if len(cropped_enhanced_list) == 0:
            cropped_enhanced_list = [empty_pil_tensor()]

        if len(cropped_enhanced_alpha_list) == 0:
            cropped_enhanced_alpha_list = [empty_pil_tensor()]

        return enhanced_img_batch, cropped_enhanced_list, cropped_enhanced_alpha_list, basic_pipe, refiner_basic_pipe_opt

```
