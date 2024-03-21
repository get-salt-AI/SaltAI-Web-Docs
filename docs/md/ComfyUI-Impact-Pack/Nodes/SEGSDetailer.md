# SEGSDetailer
## Documentation
- Class name: `SEGSDetailer`
- Category: `ImpactPack/Detailer`
- Output node: `False`

The SEGSDetailer node is designed to enhance or modify segmentation details post-detection. It operates by applying a detailer hook, if available, which can further process the segmentation results (segs) through methods like filtering or ordering, based on the specific implementation of the hook.
## Input types
### Required
- **`image`**
    - unknown
    - Python dtype: `unknown`
    - Comfy dtype: `IMAGE`
- **`segs`**
    - The input 'segs' represents the segmentation results that the detailer hook will process. This input is crucial for the operation of the SEGSDetailer, as it provides the base data for enhancement or modification.
    - Python dtype: `Tuple[Tuple[int, int], List[SEG_ELT]]`
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
- **`inpaint_model`**
    - unknown
    - Python dtype: `unknown`
    - Comfy dtype: `BOOLEAN`
- **`noise_mask_feather`**
    - unknown
    - Python dtype: `unknown`
    - Comfy dtype: `INT`
## Output types
- **`segs`**
    - The output 'segs' contains the enhanced or modified segmentation results after applying the detailer hook. It reflects the changes made to the input 'segs', showcasing the node's ability to refine segmentation details.
    - Python dtype: `Tuple[Tuple[int, int], List[SEG_ELT]]`
    - Comfy dtype: `SEGS`
- **`image`**
    - unknown
    - Python dtype: `unknown`
    - Comfy dtype: `IMAGE`
## Usage tips
- Infra type: `CPU`
- Common nodes: unknown


## Source code
```python
class SEGSDetailer:
    @classmethod
    def INPUT_TYPES(s):
        return {"required": {
                     "image": ("IMAGE", ),
                     "segs": ("SEGS", ),
                     "guide_size": ("FLOAT", {"default": 256, "min": 64, "max": MAX_RESOLUTION, "step": 8}),
                     "guide_size_for": ("BOOLEAN", {"default": True, "label_on": "bbox", "label_off": "crop_region"}),
                     "max_size": ("FLOAT", {"default": 768, "min": 64, "max": MAX_RESOLUTION, "step": 8}),
                     "seed": ("INT", {"default": 0, "min": 0, "max": 0xffffffffffffffff}),
                     "steps": ("INT", {"default": 20, "min": 1, "max": 10000}),
                     "cfg": ("FLOAT", {"default": 8.0, "min": 0.0, "max": 100.0}),
                     "sampler_name": (comfy.samplers.KSampler.SAMPLERS,),
                     "scheduler": (comfy.samplers.KSampler.SCHEDULERS,),
                     "denoise": ("FLOAT", {"default": 0.5, "min": 0.0001, "max": 1.0, "step": 0.01}),
                     "noise_mask": ("BOOLEAN", {"default": True, "label_on": "enabled", "label_off": "disabled"}),
                     "force_inpaint": ("BOOLEAN", {"default": False, "label_on": "enabled", "label_off": "disabled"}),
                     "basic_pipe": ("BASIC_PIPE",),
                     "refiner_ratio": ("FLOAT", {"default": 0.2, "min": 0.0, "max": 1.0}),
                     "batch_size": ("INT", {"default": 1, "min": 1, "max": 100}),

                     "cycle": ("INT", {"default": 1, "min": 1, "max": 10, "step": 1}),
                     },
                "optional": {
                     "refiner_basic_pipe_opt": ("BASIC_PIPE",),
                     "inpaint_model": ("BOOLEAN", {"default": False, "label_on": "enabled", "label_off": "disabled"}),
                     "noise_mask_feather": ("INT", {"default": 0, "min": 0, "max": 100, "step": 1}),
                     }
                }

    RETURN_TYPES = ("SEGS", "IMAGE")
    RETURN_NAMES = ("segs", "cnet_images")
    OUTPUT_IS_LIST = (False, True)

    FUNCTION = "doit"

    CATEGORY = "ImpactPack/Detailer"

    @staticmethod
    def do_detail(image, segs, guide_size, guide_size_for, max_size, seed, steps, cfg, sampler_name, scheduler,
                  denoise, noise_mask, force_inpaint, basic_pipe, refiner_ratio=None, batch_size=1, cycle=1,
                  refiner_basic_pipe_opt=None, inpaint_model=False, noise_mask_feather=0):

        model, clip, vae, positive, negative = basic_pipe
        if refiner_basic_pipe_opt is None:
            refiner_model, refiner_clip, refiner_positive, refiner_negative = None, None, None, None
        else:
            refiner_model, refiner_clip, _, refiner_positive, refiner_negative = refiner_basic_pipe_opt

        segs = core.segs_scale_match(segs, image.shape)

        new_segs = []
        cnet_pil_list = []

        for i in range(batch_size):
            seed += 1
            for seg in segs[1]:
                cropped_image = seg.cropped_image if seg.cropped_image is not None \
                                                  else crop_ndarray4(image.numpy(), seg.crop_region)
                cropped_image = to_tensor(cropped_image)

                is_mask_all_zeros = (seg.cropped_mask == 0).all().item()
                if is_mask_all_zeros:
                    print(f"Detailer: segment skip [empty mask]")
                    new_segs.append(seg)
                    continue

                if noise_mask:
                    cropped_mask = seg.cropped_mask
                else:
                    cropped_mask = None

                enhanced_image, cnet_pils = core.enhance_detail(cropped_image, model, clip, vae, guide_size, guide_size_for, max_size,
                                                                seg.bbox, seed, steps, cfg, sampler_name, scheduler,
                                                                positive, negative, denoise, cropped_mask, force_inpaint,
                                                                refiner_ratio=refiner_ratio, refiner_model=refiner_model,
                                                                refiner_clip=refiner_clip, refiner_positive=refiner_positive, refiner_negative=refiner_negative,
                                                                control_net_wrapper=seg.control_net_wrapper, cycle=cycle,
                                                                inpaint_model=inpaint_model, noise_mask_feather=noise_mask_feather)

                if cnet_pils is not None:
                    cnet_pil_list.extend(cnet_pils)

                if enhanced_image is None:
                    new_cropped_image = cropped_image
                else:
                    new_cropped_image = enhanced_image

                new_seg = SEG(to_numpy(new_cropped_image), seg.cropped_mask, seg.confidence, seg.crop_region, seg.bbox, seg.label, None)
                new_segs.append(new_seg)

        return (segs[0], new_segs), cnet_pil_list

    def doit(self, image, segs, guide_size, guide_size_for, max_size, seed, steps, cfg, sampler_name, scheduler,
             denoise, noise_mask, force_inpaint, basic_pipe, refiner_ratio=None, batch_size=1, cycle=1,
             refiner_basic_pipe_opt=None, inpaint_model=False, noise_mask_feather=0):

        if len(image) > 1:
            raise Exception('[Impact Pack] ERROR: SEGSDetailer does not allow image batches.\nPlease refer to https://github.com/ltdrdata/ComfyUI-extension-tutorials/blob/Main/ComfyUI-Impact-Pack/tutorial/batching-detailer.md for more information.')

        segs, cnet_pil_list = SEGSDetailer.do_detail(image, segs, guide_size, guide_size_for, max_size, seed, steps, cfg, sampler_name,
                                                     scheduler, denoise, noise_mask, force_inpaint, basic_pipe, refiner_ratio, batch_size, cycle=cycle,
                                                     refiner_basic_pipe_opt=refiner_basic_pipe_opt,
                                                     inpaint_model=inpaint_model, noise_mask_feather=noise_mask_feather)

        # set fallback image
        if len(cnet_pil_list) == 0:
            cnet_pil_list = [empty_pil_tensor()]

        return (segs, cnet_pil_list)

```
