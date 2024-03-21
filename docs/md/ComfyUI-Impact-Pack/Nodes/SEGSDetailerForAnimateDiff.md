# SEGSDetailer For AnimateDiff (SEGS/pipe)
## Documentation
- Class name: `SEGSDetailerForAnimateDiff`
- Category: `ImpactPack/Detailer`
- Output node: `False`

The SEGSDetailerForAnimateDiff node is designed to enhance the segmentation details for animation difference processing. It takes in image frames and segmentation data, applies detailed processing to improve the segmentation quality, and returns the enhanced segmentation data. This node is part of the ImpactPack/Detailer category, focusing on refining segmentation for animated content by adjusting various parameters like guide size, max size, seed, steps, and more.
## Input types
### Required
- **`image_frames`**
    - Image frames to be processed for animation difference. These frames are the basis for segmentation enhancement.
    - Python dtype: `List[torch.Tensor]`
    - Comfy dtype: `IMAGE`
- **`segs`**
    - Initial segmentation data that will be refined by the node. This data is crucial for identifying areas of interest in the animation frames.
    - Python dtype: `Tuple[torch.Tensor, List[torch.Tensor]]`
    - Comfy dtype: `SEGS`
- **`guide_size`**
    - A parameter influencing the level of detail in the segmentation refinement process. It adjusts the guiding size for the detail enhancement.
    - Python dtype: `int`
    - Comfy dtype: `INT`
- **`guide_size_for`**
    - Specifies the target for the guide size adjustment, affecting how the detail refinement is applied.
    - Python dtype: `str`
    - Comfy dtype: `STRING`
- **`max_size`**
    - Sets the maximum size limit for the segmentation detail enhancement, ensuring the process stays within computational bounds.
    - Python dtype: `int`
    - Comfy dtype: `INT`
- **`seed`**
    - Seed for random number generation, ensuring reproducibility of the segmentation enhancement.
    - Python dtype: `int`
    - Comfy dtype: `INT`
- **`steps`**
    - Defines the number of steps in the detail enhancement process, controlling the depth of refinement.
    - Python dtype: `int`
    - Comfy dtype: `INT`
- **`cfg`**
    - Configuration settings for the detail enhancement process, allowing customization of the refinement.
    - Python dtype: `str`
    - Comfy dtype: `STRING`
- **`sampler_name`**
    - Specifies the sampler to be used in the detail enhancement process, affecting the quality of the output.
    - Python dtype: `str`
    - Comfy dtype: `STRING`
- **`scheduler`**
    - Determines the scheduling strategy for the detail enhancement steps, optimizing the process flow.
    - Python dtype: `str`
    - Comfy dtype: `STRING`
- **`denoise`**
    - A flag indicating whether denoising should be applied during the detail enhancement, improving the quality of the output.
    - Python dtype: `bool`
    - Comfy dtype: `BOOLEAN`
- **`basic_pipe`**
    - The basic pipeline configuration for the detail enhancement process, defining the overall workflow.
    - Python dtype: `str`
    - Comfy dtype: `STRING`
- **`refiner_ratio`**
    - Optional parameter to adjust the ratio of refinement applied, allowing for finer control over the enhancement.
    - Python dtype: `Optional[float]`
    - Comfy dtype: `FLOAT`
### Optional
- **`refiner_basic_pipe_opt`**
    - Optional parameter to specify an alternative basic pipeline for refinement, offering flexibility in the process.
    - Python dtype: `Optional[str]`
    - Comfy dtype: `STRING`
## Output types
- **`segs`**
    - The enhanced segmentation data, resulting from the detailed processing applied to the initial segmentation. This output is crucial for further animation difference processing.
    - Python dtype: `Tuple[torch.Tensor, List[torch.Tensor]]`
    - Comfy dtype: `SEGS`
## Usage tips
- Infra type: `GPU`
- Common nodes: `SEGSPaste`


## Source code
```python
class SEGSDetailerForAnimateDiff:
    @classmethod
    def INPUT_TYPES(cls):
        return {"required": {
                     "image_frames": ("IMAGE", ),
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
                     "basic_pipe": ("BASIC_PIPE",),
                     "refiner_ratio": ("FLOAT", {"default": 0.2, "min": 0.0, "max": 1.0})
                     },
                "optional": {
                     "refiner_basic_pipe_opt": ("BASIC_PIPE",),
                     # TODO: "inpaint_model": ("BOOLEAN", {"default": False, "label_on": "enabled", "label_off": "disabled"}),
                     # TODO: "noise_mask_feather": ("INT", {"default": 0, "min": 0, "max": 100, "step": 1}),
                     }
                }

    RETURN_TYPES = ("SEGS",)
    RETURN_NAMES = ("segs",)
    OUTPUT_IS_LIST = (False,)

    FUNCTION = "doit"

    CATEGORY = "ImpactPack/Detailer"

    @staticmethod
    def do_detail(image_frames, segs, guide_size, guide_size_for, max_size, seed, steps, cfg, sampler_name, scheduler,
                  denoise, basic_pipe, refiner_ratio=None, refiner_basic_pipe_opt=None, inpaint_model=False, noise_mask_feather=0):

        model, clip, vae, positive, negative = basic_pipe
        if refiner_basic_pipe_opt is None:
            refiner_model, refiner_clip, refiner_positive, refiner_negative = None, None, None, None
        else:
            refiner_model, refiner_clip, _, refiner_positive, refiner_negative = refiner_basic_pipe_opt

        segs = core.segs_scale_match(segs, image_frames.shape)

        new_segs = []

        for seg in segs[1]:
            cropped_image_frames = None

            for image in image_frames:
                image = image.unsqueeze(0)
                cropped_image = seg.cropped_image if seg.cropped_image is not None else crop_tensor4(image, seg.crop_region)
                cropped_image = to_tensor(cropped_image)
                if cropped_image_frames is None:
                    cropped_image_frames = cropped_image
                else:
                    cropped_image_frames = torch.concat((cropped_image_frames, cropped_image), dim=0)

            cropped_image_frames = cropped_image_frames.numpy()
            enhanced_image_tensor = core.enhance_detail_for_animatediff(cropped_image_frames, model, clip, vae, guide_size, guide_size_for, max_size,
                                                                        seg.bbox, seed, steps, cfg, sampler_name, scheduler,
                                                                        positive, negative, denoise, seg.cropped_mask,
                                                                        refiner_ratio=refiner_ratio, refiner_model=refiner_model,
                                                                        refiner_clip=refiner_clip, refiner_positive=refiner_positive,
                                                                        refiner_negative=refiner_negative,
                                                                        inpaint_model=inpaint_model, noise_mask_feather=noise_mask_feather)

            if enhanced_image_tensor is None:
                new_cropped_image = cropped_image_frames
            else:
                new_cropped_image = enhanced_image_tensor.numpy()

            new_seg = SEG(new_cropped_image, seg.cropped_mask, seg.confidence, seg.crop_region, seg.bbox, seg.label, None)
            new_segs.append(new_seg)

        return (segs[0], new_segs)

    def doit(self, image_frames, segs, guide_size, guide_size_for, max_size, seed, steps, cfg, sampler_name, scheduler,
             denoise, basic_pipe, refiner_ratio=None, refiner_basic_pipe_opt=None, inpaint_model=False, noise_mask_feather=0):

        segs = SEGSDetailerForAnimateDiff.do_detail(image_frames, segs, guide_size, guide_size_for, max_size, seed, steps, cfg, sampler_name,
                                                    scheduler, denoise, basic_pipe, refiner_ratio, refiner_basic_pipe_opt,
                                                    inpaint_model=inpaint_model, noise_mask_feather=noise_mask_feather)

        return (segs,)

```
