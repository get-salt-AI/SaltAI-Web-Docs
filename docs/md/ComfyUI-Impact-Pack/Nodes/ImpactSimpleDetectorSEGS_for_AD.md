# Simple Detector for AnimateDiff (SEGS)
## Documentation
- Class name: `ImpactSimpleDetectorSEGS_for_AD`
- Category: `ImpactPack/Detector`
- Output node: `False`

The `ImpactSimpleDetectorSEGS_for_AD` node is designed for detecting and segmenting objects in images specifically tailored for animations and differences in sequences. It leverages both bounding box and segmentation models to identify objects across frames, applying various post-processing techniques to refine segmentation masks. This functionality is particularly useful for animated content, where precise object delineation is crucial.
## Input types
### Required
- **`bbox_detector`**
    - The bounding box detector used for initial object detection in the image frames. It serves as the foundation for subsequent segmentation and refinement processes.
    - Python dtype: `BBOX_DETECTOR`
    - Comfy dtype: `BBOX_DETECTOR`
- **`image_frames`**
    - A sequence of images on which object detection and segmentation are performed. The sequence's quality and consistency significantly impact the detection results.
    - Python dtype: `torch.Tensor`
    - Comfy dtype: `IMAGE`
- **`bbox_threshold`**
    - A threshold value for filtering bounding box detection results based on confidence scores. Adjusting this value affects the initial object detection phase.
    - Python dtype: `float`
    - Comfy dtype: `FLOAT`
- **`bbox_dilation`**
    - The amount of dilation applied to the bounding boxes before segmentation. This parameter helps in adjusting the area considered for segmentation.
    - Python dtype: `int`
    - Comfy dtype: `INT`
- **`crop_factor`**
    - Determines how much the area around the detected bounding box is expanded or contracted during the cropping phase for segmentation.
    - Python dtype: `float`
    - Comfy dtype: `FLOAT`
- **`drop_size`**
    - Specifies the minimum size for detected objects to be considered valid. Objects smaller than this size are ignored.
    - Python dtype: `int`
    - Comfy dtype: `INT`
- **`sub_threshold`**
    - A threshold value used for filtering segmentation results in the sub-processing phase, based on confidence scores.
    - Python dtype: `float`
    - Comfy dtype: `FLOAT`
- **`sub_dilation`**
    - The amount of dilation applied to segmentation masks during the sub-processing phase. This parameter can be used to refine the segmentation results.
    - Python dtype: `int`
    - Comfy dtype: `INT`
- **`sub_bbox_expansion`**
    - Determines the expansion of bounding boxes during the sub-processing phase, affecting the area considered for further segmentation.
    - Python dtype: `float`
    - Comfy dtype: `FLOAT`
- **`sam_mask_hint_threshold`**
    - A threshold value used when applying SAM (Segmentation-Aware Masking) models to refine segmentation masks based on hints.
    - Python dtype: `float`
    - Comfy dtype: `FLOAT`
### Optional
- **`masking_mode`**
    - Specifies the mode of masking to be applied, which can influence the segmentation process and results.
    - Python dtype: `str`
    - Comfy dtype: `STRING`
- **`segs_pivot`**
    - Determines the pivot for segmentation operations, affecting how segmentation masks are combined or processed.
    - Python dtype: `str`
    - Comfy dtype: `STRING`
- **`sam_model_opt`**
    - Optional SAM model used for advanced segmentation masking and refinement.
    - Python dtype: `Optional[SAM_MODEL]`
    - Comfy dtype: `SAM_MODEL`
- **`segm_detector_opt`**
    - Optional segmentation detector used for additional segmentation processing and refinement.
    - Python dtype: `Optional[SEGM_DETECTOR]`
    - Comfy dtype: `SEGM_DETECTOR`
## Output types
- **`segs`**
    - The segmented objects identified across the input image frames, represented as a collection of segmentation masks along with their respective confidence scores and bounding boxes.
    - Python dtype: `Tuple[torch.Size, List[SEG]]`
    - Comfy dtype: `SEGS`
## Usage tips
- Infra type: `GPU`
- Common nodes: `DetailerForEachPipeForAnimateDiff,ImpactControlNetApplySEGS`


## Source code
```python
class SimpleDetectorForAnimateDiff:
    @classmethod
    def INPUT_TYPES(s):
        return {"required": {
                        "bbox_detector": ("BBOX_DETECTOR", ),
                        "image_frames": ("IMAGE", ),

                        "bbox_threshold": ("FLOAT", {"default": 0.5, "min": 0.0, "max": 1.0, "step": 0.01}),
                        "bbox_dilation": ("INT", {"default": 0, "min": -255, "max": 255, "step": 1}),

                        "crop_factor": ("FLOAT", {"default": 3.0, "min": 1.0, "max": 100, "step": 0.1}),
                        "drop_size": ("INT", {"min": 1, "max": MAX_RESOLUTION, "step": 1, "default": 10}),

                        "sub_threshold": ("FLOAT", {"default": 0.5, "min": 0.0, "max": 1.0, "step": 0.01}),
                        "sub_dilation": ("INT", {"default": 0, "min": -255, "max": 255, "step": 1}),
                        "sub_bbox_expansion": ("INT", {"default": 0, "min": 0, "max": 1000, "step": 1}),

                        "sam_mask_hint_threshold": ("FLOAT", {"default": 0.7, "min": 0.0, "max": 1.0, "step": 0.01}),
                      },
                "optional": {
                        "masking_mode": (["Pivot SEGS", "Combine neighboring frames", "Don't combine"],),
                        "segs_pivot": (["Combined mask", "1st frame mask"],),
                        "sam_model_opt": ("SAM_MODEL", ),
                        "segm_detector_opt": ("SEGM_DETECTOR", ),
                 }
                }

    RETURN_TYPES = ("SEGS",)
    FUNCTION = "doit"

    CATEGORY = "ImpactPack/Detector"

    @staticmethod
    def detect(bbox_detector, image_frames, bbox_threshold, bbox_dilation, crop_factor, drop_size,
               sub_threshold, sub_dilation, sub_bbox_expansion, sam_mask_hint_threshold,
               masking_mode="Pivot SEGS", segs_pivot="Combined mask", sam_model_opt=None, segm_detector_opt=None):

        h = image_frames.shape[1]
        w = image_frames.shape[2]

        # gather segs for all frames
        segs_by_frames = []
        for image in image_frames:
            image = image.unsqueeze(0)
            segs = bbox_detector.detect(image, bbox_threshold, bbox_dilation, crop_factor, drop_size)

            if sam_model_opt is not None:
                mask = core.make_sam_mask(sam_model_opt, segs, image, "center-1", sub_dilation,
                                          sub_threshold, sub_bbox_expansion, sam_mask_hint_threshold, False)
                segs = core.segs_bitwise_and_mask(segs, mask)
            elif segm_detector_opt is not None:
                segm_segs = segm_detector_opt.detect(image, sub_threshold, sub_dilation, crop_factor, drop_size)
                mask = core.segs_to_combined_mask(segm_segs)
                segs = core.segs_bitwise_and_mask(segs, mask)

            segs_by_frames.append(segs)

        def get_masked_frames():
            masks_by_frame = []
            for i, segs in enumerate(segs_by_frames):
                masks_in_frame = segs_nodes.SEGSToMaskList().doit(segs)[0]
                current_frame_mask = (masks_in_frame[0] * 255).to(torch.uint8)

                for mask in masks_in_frame[1:]:
                    current_frame_mask |= (mask * 255).to(torch.uint8)

                current_frame_mask = (current_frame_mask/255.0).to(torch.float32)
                current_frame_mask = utils.to_binary_mask(current_frame_mask, 0.1)[0]

                masks_by_frame.append(current_frame_mask)

            return masks_by_frame

        def get_empty_mask():
            return torch.zeros((h, w), dtype=torch.float32, device="cpu")

        def get_neighboring_mask_at(i, masks_by_frame):
            prv = masks_by_frame[i-1] if i > 1 else get_empty_mask()
            cur = masks_by_frame[i]
            nxt = masks_by_frame[i-1] if i > 1 else get_empty_mask()

            prv = prv if prv is not None else get_empty_mask()
            cur = cur.clone() if cur is not None else get_empty_mask()
            nxt = nxt if nxt is not None else get_empty_mask()

            return prv, cur, nxt

        def get_merged_neighboring_mask(masks_by_frame):
            if len(masks_by_frame) <= 1:
                return masks_by_frame

            result = []
            for i in range(0, len(masks_by_frame)):
                prv, cur, nxt = get_neighboring_mask_at(i, masks_by_frame)
                cur = (cur * 255).to(torch.uint8)
                cur |= (prv * 255).to(torch.uint8)
                cur |= (nxt * 255).to(torch.uint8)
                cur = (cur / 255.0).to(torch.float32)
                cur = utils.to_binary_mask(cur, 0.1)[0]
                result.append(cur)

            return result

        def get_whole_merged_mask():
            all_masks = []
            for segs in segs_by_frames:
                all_masks += segs_nodes.SEGSToMaskList().doit(segs)[0]

            merged_mask = (all_masks[0] * 255).to(torch.uint8)
            for mask in all_masks[1:]:
                merged_mask |= (mask * 255).to(torch.uint8)

            merged_mask = (merged_mask / 255.0).to(torch.float32)
            merged_mask = utils.to_binary_mask(merged_mask, 0.1)[0]
            return merged_mask

        def get_pivot_segs():
            if segs_pivot == "1st frame mask":
                return segs_by_frames[0][1]
            else:
                merged_mask = get_whole_merged_mask()
                return segs_nodes.MaskToSEGS().doit(merged_mask, False, crop_factor, False, drop_size, contour_fill=True)[0]

        def get_merged_neighboring_segs():
            pivot_segs = get_pivot_segs()

            masks_by_frame = get_masked_frames()
            masks_by_frame = get_merged_neighboring_mask(masks_by_frame)

            new_segs = []
            for seg in pivot_segs[1]:
                cropped_mask = torch.zeros(seg.cropped_mask.shape, dtype=torch.float32, device="cpu").unsqueeze(0)
                pivot_mask = torch.from_numpy(seg.cropped_mask)
                x1, y1, x2, y2 = seg.crop_region
                for mask in masks_by_frame:
                    cropped_mask_at_frame = (mask[y1:y2, x1:x2] * pivot_mask).unsqueeze(0)
                    cropped_mask = torch.cat((cropped_mask, cropped_mask_at_frame), dim=0)

                if len(cropped_mask) > 1:
                    cropped_mask = cropped_mask[1:]

                new_seg = SEG(seg.cropped_image, cropped_mask, seg.confidence, seg.crop_region, seg.bbox, seg.label, seg.control_net_wrapper)
                new_segs.append(new_seg)

            return pivot_segs[0], new_segs

        def get_separated_segs():
            pivot_segs = get_pivot_segs()

            masks_by_frame = get_masked_frames()

            new_segs = []
            for seg in pivot_segs[1]:
                cropped_mask = torch.zeros(seg.cropped_mask.shape, dtype=torch.float32, device="cpu").unsqueeze(0)
                x1, y1, x2, y2 = seg.crop_region
                for mask in masks_by_frame:
                    cropped_mask_at_frame = mask[y1:y2, x1:x2]
                    cropped_mask = torch.cat((cropped_mask, cropped_mask_at_frame), dim=0)

                new_seg = SEG(seg.cropped_image, cropped_mask, seg.confidence, seg.crop_region, seg.bbox, seg.label, seg.control_net_wrapper)
                new_segs.append(new_seg)

            return pivot_segs[0], new_segs

        # create result mask
        if masking_mode == "Pivot SEGS":
            return (get_pivot_segs(), )

        elif masking_mode == "Combine neighboring frames":
            return (get_merged_neighboring_segs(), )

        else: # elif masking_mode == "Don't combine":
            return (get_separated_segs(), )

    def doit(self, bbox_detector, image_frames, bbox_threshold, bbox_dilation, crop_factor, drop_size,
             sub_threshold, sub_dilation, sub_bbox_expansion, sam_mask_hint_threshold,
             masking_mode="Pivot SEGS", segs_pivot="Combined mask", sam_model_opt=None, segm_detector_opt=None):

        return SimpleDetectorForAnimateDiff.detect(bbox_detector, image_frames, bbox_threshold, bbox_dilation, crop_factor, drop_size,
                                                   sub_threshold, sub_dilation, sub_bbox_expansion, sam_mask_hint_threshold,
                                                   masking_mode, segs_pivot, sam_model_opt, segm_detector_opt)

```
