# Simple Detector (SEGS)
## Documentation
- Class name: `ImpactSimpleDetectorSEGS`
- Category: `ImpactPack/Detector`
- Output node: `False`

The ImpactSimpleDetectorSEGS node is designed for detecting objects in images using segmentation models. It processes an image to identify and segment objects, returning segmented regions as output. This node is part of the ImpactPack's suite of nodes for image analysis and manipulation, specifically tailored for tasks involving segmentation.
## Input types
### Required
- **`bbox_detector`**
    - The bounding box detector used for initial object detection in the image. It's crucial for identifying potential objects to be segmented.
    - Python dtype: `torch.nn.Module`
    - Comfy dtype: `BBOX_DETECTOR`
- **`image`**
    - unknown
    - Python dtype: `unknown`
    - Comfy dtype: `IMAGE`
- **`bbox_threshold`**
    - A threshold value for the bounding box detector to determine the cut-off point for object detection. It fine-tunes the sensitivity of the detection process.
    - Python dtype: `float`
    - Comfy dtype: `FLOAT`
- **`bbox_dilation`**
    - Controls the extent to which the detected bounding boxes are expanded, potentially capturing more of the object for accurate segmentation.
    - Python dtype: `int`
    - Comfy dtype: `INT`
- **`crop_factor`**
    - Determines how much the detected object's bounding box is enlarged or reduced during the cropping process, affecting the segmentation's focus area.
    - Python dtype: `float`
    - Comfy dtype: `FLOAT`
- **`drop_size`**
    - Specifies the minimum size for detected objects to be considered valid, filtering out objects that are too small.
    - Python dtype: `int`
    - Comfy dtype: `INT`
- **`sub_threshold`**
    - A threshold value used for fine-tuning the sensitivity of the sub-detection process, specifically for segmentation.
    - Python dtype: `float`
    - Comfy dtype: `FLOAT`
- **`sub_dilation`**
    - Controls the extent to which the segmented regions from the sub-detection process are expanded.
    - Python dtype: `int`
    - Comfy dtype: `INT`
- **`sub_bbox_expansion`**
    - Determines the expansion of bounding boxes in the sub-detection process, potentially capturing more details of the object.
    - Python dtype: `float`
    - Comfy dtype: `FLOAT`
- **`sam_mask_hint_threshold`**
    - A threshold value used in generating SAM masks, which are hints for the segmentation process to focus on specific areas.
    - Python dtype: `float`
    - Comfy dtype: `FLOAT`
### Optional
- **`post_dilation`**
    - Applies additional dilation to the segmented regions after the main segmentation process, enhancing the final output.
    - Python dtype: `int`
    - Comfy dtype: `INT`
- **`sam_model_opt`**
    - Optional SAM model used for generating SAM masks, providing hints for the segmentation process.
    - Python dtype: `torch.nn.Module`
    - Comfy dtype: `SAM_MODEL`
- **`segm_detector_opt`**
    - Optional segmentation detector used for enhancing the segmentation process with additional segmentation models.
    - Python dtype: `torch.nn.Module`
    - Comfy dtype: `SEGM_DETECTOR`
## Output types
- **`segs`**
    - The output segmented regions of the image. Each region corresponds to a detected object, providing valuable information for further analysis or processing.
    - Python dtype: `List[torch.Tensor]`
    - Comfy dtype: `SEGS`
## Usage tips
- Infra type: `GPU`
- Common nodes: `ImpactMakeTileSEGS,ImpactControlNetApplySEGS,SegsToCombinedMask`


## Source code
```python
class SimpleDetectorForEach:
    @classmethod
    def INPUT_TYPES(s):
        return {"required": {
                        "bbox_detector": ("BBOX_DETECTOR", ),
                        "image": ("IMAGE", ),

                        "bbox_threshold": ("FLOAT", {"default": 0.5, "min": 0.0, "max": 1.0, "step": 0.01}),
                        "bbox_dilation": ("INT", {"default": 0, "min": -512, "max": 512, "step": 1}),

                        "crop_factor": ("FLOAT", {"default": 3.0, "min": 1.0, "max": 100, "step": 0.1}),
                        "drop_size": ("INT", {"min": 1, "max": MAX_RESOLUTION, "step": 1, "default": 10}),

                        "sub_threshold": ("FLOAT", {"default": 0.5, "min": 0.0, "max": 1.0, "step": 0.01}),
                        "sub_dilation": ("INT", {"default": 0, "min": -512, "max": 512, "step": 1}),
                        "sub_bbox_expansion": ("INT", {"default": 0, "min": 0, "max": 1000, "step": 1}),

                        "sam_mask_hint_threshold": ("FLOAT", {"default": 0.7, "min": 0.0, "max": 1.0, "step": 0.01}),
                      },
                "optional": {
                        "post_dilation": ("INT", {"default": 0, "min": -512, "max": 512, "step": 1}),
                        "sam_model_opt": ("SAM_MODEL", ),
                        "segm_detector_opt": ("SEGM_DETECTOR", ),
                      }
                }

    RETURN_TYPES = ("SEGS",)
    FUNCTION = "doit"

    CATEGORY = "ImpactPack/Detector"

    @staticmethod
    def detect(bbox_detector, image, bbox_threshold, bbox_dilation, crop_factor, drop_size,
               sub_threshold, sub_dilation, sub_bbox_expansion,
               sam_mask_hint_threshold, post_dilation=0, sam_model_opt=None, segm_detector_opt=None,
               detailer_hook=None):
        if len(image) > 1:
            raise Exception('[Impact Pack] ERROR: SimpleDetectorForEach does not allow image batches.\nPlease refer to https://github.com/ltdrdata/ComfyUI-extension-tutorials/blob/Main/ComfyUI-Impact-Pack/tutorial/batching-detailer.md for more information.')

        segs = bbox_detector.detect(image, bbox_threshold, bbox_dilation, crop_factor, drop_size, detailer_hook=detailer_hook)

        if sam_model_opt is not None:
            mask = core.make_sam_mask(sam_model_opt, segs, image, "center-1", sub_dilation,
                                      sub_threshold, sub_bbox_expansion, sam_mask_hint_threshold, False)
            segs = core.segs_bitwise_and_mask(segs, mask)
        elif segm_detector_opt is not None:
            segm_segs = segm_detector_opt.detect(image, sub_threshold, sub_dilation, crop_factor, drop_size, detailer_hook=detailer_hook)
            mask = core.segs_to_combined_mask(segm_segs)
            segs = core.segs_bitwise_and_mask(segs, mask)

        segs = core.dilate_segs(segs, post_dilation)

        return (segs,)

    def doit(self, bbox_detector, image, bbox_threshold, bbox_dilation, crop_factor, drop_size,
             sub_threshold, sub_dilation, sub_bbox_expansion,
             sam_mask_hint_threshold, post_dilation=0, sam_model_opt=None, segm_detector_opt=None):

        return SimpleDetectorForEach.detect(bbox_detector, image, bbox_threshold, bbox_dilation, crop_factor, drop_size,
                                            sub_threshold, sub_dilation, sub_bbox_expansion,
                                            sam_mask_hint_threshold, post_dilation=post_dilation,
                                            sam_model_opt=sam_model_opt, segm_detector_opt=segm_detector_opt)

```
