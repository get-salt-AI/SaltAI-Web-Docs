# FaceDetailer (pipe)
## Documentation
- Class name: `FaceDetailerPipe`
- Category: `ImpactPack/Simple`
- Output node: `False`

The FaceDetailerPipe class is designed to enhance the details of faces in images. It utilizes advanced image processing techniques to refine facial features, making them more pronounced and visually appealing.
## Input types
### Required
- **`image`**
    - unknown
    - Python dtype: `unknown`
    - Comfy dtype: `IMAGE`
- **`detailer_pipe`**
    - Specifies the pipeline configuration for face detailing, including models and parameters to be used. This setup is essential for tailoring the face detailing process to specific needs.
    - Python dtype: `Tuple[Optional[torch.Tensor], Optional[torch.Tensor], Optional[torch.Tensor], Optional[str], Optional[str], Optional[str], Optional[str], Optional[str], Optional[str], Optional[str], Optional[str], Optional[str], Optional[str], Optional[str]]`
    - Comfy dtype: `DETAILER_PIPE`
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
- **`bbox_threshold`**
    - unknown
    - Python dtype: `unknown`
    - Comfy dtype: `FLOAT`
- **`bbox_dilation`**
    - unknown
    - Python dtype: `unknown`
    - Comfy dtype: `INT`
- **`bbox_crop_factor`**
    - unknown
    - Python dtype: `unknown`
    - Comfy dtype: `FLOAT`
- **`sam_detection_hint`**
    - unknown
    - Python dtype: `unknown`
    - Comfy dtype: `['center-1', 'horizontal-2', 'vertical-2'...]`
- **`sam_dilation`**
    - unknown
    - Python dtype: `unknown`
    - Comfy dtype: `INT`
- **`sam_threshold`**
    - unknown
    - Python dtype: `unknown`
    - Comfy dtype: `FLOAT`
- **`sam_bbox_expansion`**
    - unknown
    - Python dtype: `unknown`
    - Comfy dtype: `INT`
- **`sam_mask_hint_threshold`**
    - unknown
    - Python dtype: `unknown`
    - Comfy dtype: `FLOAT`
- **`sam_mask_hint_use_negative`**
    - unknown
    - Python dtype: `unknown`
    - Comfy dtype: `['False', 'Small', 'Outter'...]`
- **`drop_size`**
    - unknown
    - Python dtype: `unknown`
    - Comfy dtype: `INT`
- **`refiner_ratio`**
    - unknown
    - Python dtype: `unknown`
    - Comfy dtype: `FLOAT`
- **`cycle`**
    - unknown
    - Python dtype: `unknown`
    - Comfy dtype: `INT`
### Optional
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
- **`mask`**
    - unknown
    - Python dtype: `unknown`
    - Comfy dtype: `MASK`
- **`detailer_pipe`**
    - unknown
    - Python dtype: `unknown`
    - Comfy dtype: `DETAILER_PIPE`
- **`ui`**
    - Provides a user interface component for visualizing and interacting with the face detailing results. This output enhances the usability and accessibility of the face detailing process.
## Usage tips
- Infra type: `GPU`
- Common nodes: `PreviewImage,MaskToImage,PlaySound|pysssss,Reroute,SaveImage,ReroutePrimitive|pysssss,DetailerForEachDebugPipe,DetailerPipeToBasicPipe,ImageUpscaleWithModel,FaceDetailerPipe`


## Source code
```python
class FaceDetailerPipe:
    @classmethod
    def INPUT_TYPES(s):
        return {"required": {
                    "image": ("IMAGE", ),
                    "detailer_pipe": ("DETAILER_PIPE",),
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
                    "force_inpaint": ("BOOLEAN", {"default": False, "label_on": "enabled", "label_off": "disabled"}),

                    "bbox_threshold": ("FLOAT", {"default": 0.5, "min": 0.0, "max": 1.0, "step": 0.01}),
                    "bbox_dilation": ("INT", {"default": 10, "min": -512, "max": 512, "step": 1}),
                    "bbox_crop_factor": ("FLOAT", {"default": 3.0, "min": 1.0, "max": 10, "step": 0.1}),

                    "sam_detection_hint": (["center-1", "horizontal-2", "vertical-2", "rect-4", "diamond-4", "mask-area", "mask-points", "mask-point-bbox", "none"],),
                    "sam_dilation": ("INT", {"default": 0, "min": -512, "max": 512, "step": 1}),
                    "sam_threshold": ("FLOAT", {"default": 0.93, "min": 0.0, "max": 1.0, "step": 0.01}),
                    "sam_bbox_expansion": ("INT", {"default": 0, "min": 0, "max": 1000, "step": 1}),
                    "sam_mask_hint_threshold": ("FLOAT", {"default": 0.7, "min": 0.0, "max": 1.0, "step": 0.01}),
                    "sam_mask_hint_use_negative": (["False", "Small", "Outter"],),

                    "drop_size": ("INT", {"min": 1, "max": MAX_RESOLUTION, "step": 1, "default": 10}),
                    "refiner_ratio": ("FLOAT", {"default": 0.2, "min": 0.0, "max": 1.0}),

                    "cycle": ("INT", {"default": 1, "min": 1, "max": 10, "step": 1}),
                   },
                "optional": {
                    "inpaint_model": ("BOOLEAN", {"default": False, "label_on": "enabled", "label_off": "disabled"}),
                    "noise_mask_feather": ("INT", {"default": 0, "min": 0, "max": 100, "step": 1}),
                   }
                }

    RETURN_TYPES = ("IMAGE", "IMAGE", "IMAGE", "MASK", "DETAILER_PIPE", "IMAGE")
    RETURN_NAMES = ("image", "cropped_refined", "cropped_enhanced_alpha", "mask", "detailer_pipe", "cnet_images")
    OUTPUT_IS_LIST = (False, True, True, False, False, True)
    FUNCTION = "doit"

    CATEGORY = "ImpactPack/Simple"

    def doit(self, image, detailer_pipe, guide_size, guide_size_for, max_size, seed, steps, cfg, sampler_name, scheduler,
             denoise, feather, noise_mask, force_inpaint, bbox_threshold, bbox_dilation, bbox_crop_factor,
             sam_detection_hint, sam_dilation, sam_threshold, sam_bbox_expansion,
             sam_mask_hint_threshold, sam_mask_hint_use_negative, drop_size, refiner_ratio=None,
             cycle=1, inpaint_model=False, noise_mask_feather=0):

        result_img = None
        result_mask = None
        result_cropped_enhanced = []
        result_cropped_enhanced_alpha = []
        result_cnet_images = []

        if len(image) > 1:
            print(f"[Impact Pack] WARN: FaceDetailer is not a node designed for video detailing. If you intend to perform video detailing, please use Detailer For AnimateDiff.")

        model, clip, vae, positive, negative, wildcard, bbox_detector, segm_detector, sam_model_opt, detailer_hook, \
            refiner_model, refiner_clip, refiner_positive, refiner_negative = detailer_pipe

        for i, single_image in enumerate(image):
            enhanced_img, cropped_enhanced, cropped_enhanced_alpha, mask, cnet_pil_list = FaceDetailer.enhance_face(
                single_image.unsqueeze(0), model, clip, vae, guide_size, guide_size_for, max_size, seed + i, steps, cfg, sampler_name, scheduler,
                positive, negative, denoise, feather, noise_mask, force_inpaint,
                bbox_threshold, bbox_dilation, bbox_crop_factor,
                sam_detection_hint, sam_dilation, sam_threshold, sam_bbox_expansion, sam_mask_hint_threshold,
                sam_mask_hint_use_negative, drop_size, bbox_detector, segm_detector, sam_model_opt, wildcard, detailer_hook,
                refiner_ratio=refiner_ratio, refiner_model=refiner_model,
                refiner_clip=refiner_clip, refiner_positive=refiner_positive, refiner_negative=refiner_negative,
                cycle=cycle, inpaint_model=inpaint_model, noise_mask_feather=noise_mask_feather)

            result_img = torch.cat((result_img, enhanced_img), dim=0) if result_img is not None else enhanced_img
            result_mask = torch.cat((result_mask, mask), dim=0) if result_mask is not None else mask
            result_cropped_enhanced.extend(cropped_enhanced)
            result_cropped_enhanced_alpha.extend(cropped_enhanced_alpha)
            result_cnet_images.extend(cnet_pil_list)

        if len(result_cropped_enhanced) == 0:
            result_cropped_enhanced = [empty_pil_tensor()]

        if len(result_cropped_enhanced_alpha) == 0:
            result_cropped_enhanced_alpha = [empty_pil_tensor()]

        if len(result_cnet_images) == 0:
            result_cnet_images = [empty_pil_tensor()]

        return result_img, result_cropped_enhanced, result_cropped_enhanced_alpha, result_mask, detailer_pipe, result_cnet_images

```
