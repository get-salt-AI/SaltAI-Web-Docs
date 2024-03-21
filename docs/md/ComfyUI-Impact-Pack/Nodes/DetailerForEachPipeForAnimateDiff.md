# Detailer For AnimateDiff (SEGS/pipe)
## Documentation
- Class name: `DetailerForEachPipeForAnimateDiff`
- Category: `ImpactPack/Detailer`
- Output node: `False`

This node is designed to process each pipe specifically for animation difference tasks within the SEGS/pipe context. It likely involves iterating over a collection of pipes, applying detailed analysis or transformations to enhance or modify animations based on the SEGS framework.
## Input types
### Required
- **`image_frames`**
    - The frames of the image to be processed for animation differences. It's crucial for defining the visual content that will undergo transformation.
    - Python dtype: `List[torch.Tensor]`
    - Comfy dtype: `IMAGE`
- **`segs`**
    - Segmentation data associated with the image frames. It plays a key role in identifying and isolating specific areas for detailed processing.
    - Python dtype: `Tuple[torch.Tensor, List[torch.Tensor]]`
    - Comfy dtype: `SEGS`
- **`guide_size`**
    - Specifies the target size for guiding the detail enhancement process, affecting the granularity of details.
    - Python dtype: `float`
    - Comfy dtype: `FLOAT`
- **`guide_size_for`**
    - Determines whether the guide size is applied to the bounding box or the crop region, influencing the area of detail enhancement.
    - Python dtype: `bool`
    - Comfy dtype: `BOOLEAN`
- **`max_size`**
    - Defines the maximum size limit for the processed images, ensuring they do not exceed a certain resolution.
    - Python dtype: `float`
    - Comfy dtype: `FLOAT`
- **`seed`**
    - Seed for random number generation, ensuring reproducibility of the detail enhancement process.
    - Python dtype: `int`
    - Comfy dtype: `INT`
- **`steps`**
    - The number of steps to perform in the detail enhancement process, affecting the intensity of the modification.
    - Python dtype: `int`
    - Comfy dtype: `INT`
- **`cfg`**
    - Configuration parameter influencing the behavior of the detail enhancement algorithm.
    - Python dtype: `float`
    - Comfy dtype: `FLOAT`
- **`sampler_name`**
    - Specifies the sampler to be used in the detail enhancement process, affecting the sampling strategy.
    - Python dtype: `str`
    - Comfy dtype: `STRING`
- **`scheduler`**
    - Determines the scheduling strategy for the detail enhancement process, influencing the order of operations.
    - Python dtype: `str`
    - Comfy dtype: `STRING`
- **`denoise`**
    - The denoising factor applied during the detail enhancement, affecting the smoothness of the output.
    - Python dtype: `float`
    - Comfy dtype: `FLOAT`
- **`feather`**
    - Feathering parameter for blending edges in the enhanced segments, affecting the smoothness of transitions.
    - Python dtype: `int`
    - Comfy dtype: `INT`
- **`basic_pipe`**
    - The basic processing pipeline configuration. It influences the overall processing strategy and outcomes for the animation difference tasks.
    - Python dtype: `Tuple`
    - Comfy dtype: `BASIC_PIPE`
- **`refiner_ratio`**
    - Ratio of refinement applied to the basic pipe, adjusting the level of detail enhancement.
    - Python dtype: `float`
    - Comfy dtype: `FLOAT`
### Optional
- **`detailer_hook`**
    - Optional hook for additional processing or modifications during the detail enhancement.
    - Python dtype: `Callable`
    - Comfy dtype: `FUNCTION`
- **`refiner_basic_pipe_opt`**
    - Optional refined basic pipe configuration for further customization of the detail enhancement process.
    - Python dtype: `Tuple`
    - Comfy dtype: `BASIC_PIPE`
## Output types
- **`image`**
    - The processed image frames after applying animation difference enhancements.
    - Python dtype: `List[torch.Tensor]`
    - Comfy dtype: `IMAGE`
- **`segs`**
    - The updated segmentation data post-processing, reflecting changes made during the animation difference enhancements.
    - Python dtype: `Tuple[torch.Tensor, List[torch.Tensor]]`
    - Comfy dtype: `SEGS`
- **`basic_pipe`**
    - The basic processing pipeline configuration, potentially modified based on the processing outcomes.
    - Python dtype: `Tuple`
    - Comfy dtype: `BASIC_PIPE`
## Usage tips
- Infra type: `CPU`
- Common nodes: `VHS_VideoCombine`


## Source code
```python
class DetailerForEachPipeForAnimateDiff:
    @classmethod
    def INPUT_TYPES(cls):
        return {"required": {
                      "image_frames": ("IMAGE", ),
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
                      "basic_pipe": ("BASIC_PIPE", ),
                      "refiner_ratio": ("FLOAT", {"default": 0.2, "min": 0.0, "max": 1.0}),
                     },
                "optional": {
                     "detailer_hook": ("DETAILER_HOOK",),
                     "refiner_basic_pipe_opt": ("BASIC_PIPE",),
                     # "inpaint_model": ("BOOLEAN", {"default": False, "label_on": "enabled", "label_off": "disabled"}),
                     # "noise_mask_feather": ("INT", {"default": 0, "min": 0, "max": 100, "step": 1}),
                    }
                }

    RETURN_TYPES = ("IMAGE", "SEGS", "BASIC_PIPE")
    RETURN_NAMES = ("image", "segs", "basic_pipe")
    OUTPUT_IS_LIST = (False, False, False, True)
    FUNCTION = "doit"

    CATEGORY = "ImpactPack/Detailer"

    @staticmethod
    def doit(image_frames, segs, guide_size, guide_size_for, max_size, seed, steps, cfg, sampler_name, scheduler,
             denoise, feather, basic_pipe, refiner_ratio=None, detailer_hook=None, refiner_basic_pipe_opt=None,
             inpaint_model=False, noise_mask_feather=0):

        enhanced_segs = []
        for sub_seg in segs[1]:
            single_seg = segs[0], [sub_seg]
            enhanced_seg = SEGSDetailerForAnimateDiff().do_detail(image_frames, single_seg, guide_size, guide_size_for, max_size, seed, steps, cfg, sampler_name, scheduler,
                                                                  denoise, basic_pipe, refiner_ratio, refiner_basic_pipe_opt, inpaint_model, noise_mask_feather)

            image_frames = SEGSPaste.doit(image_frames, enhanced_seg, feather, alpha=255)[0]
            enhanced_segs += enhanced_seg[1]

        new_segs = segs[0], enhanced_segs
        return image_frames, new_segs, basic_pipe

```
