# SAMDetector (segmented)
## Documentation
- Class name: `SAMDetectorSegmented`
- Category: `ImpactPack/Detector`
- Output node: `False`

This node is designed to process segmented detection tasks using a SAM model. It integrates segmentation information with SAM model predictions to enhance detection accuracy, particularly useful for tasks requiring detailed segmentation analysis.
## Input types
### Required
- **`sam_model`**
    - The SAM model used for detection. It plays a crucial role in analyzing the image and segmentation data to produce enhanced detection results.
    - Python dtype: `torch.nn.Module`
    - Comfy dtype: `SAM_MODEL`
- **`segs`**
    - Segmentation data that provides detailed information about the different segments within the image. This data is essential for the SAM model to accurately perform detection.
    - Python dtype: `List[torch.Tensor]`
    - Comfy dtype: `SEGS`
- **`image`**
    - The input image to be processed. It serves as the primary data source for the detection task.
    - Python dtype: `torch.Tensor`
    - Comfy dtype: `IMAGE`
- **`detection_hint`**
    - Hints to guide the detection process, such as preferred detection regions or patterns. These hints can significantly influence the detection outcome.
    - Python dtype: `str`
    - Comfy dtype: `STRING`
- **`dilation`**
    - The dilation parameter adjusts the size of the segments or detection areas, allowing for more flexible detection boundaries.
    - Python dtype: `int`
    - Comfy dtype: `INT`
- **`threshold`**
    - A threshold value to determine the sensitivity of the detection. It helps in filtering out less likely detections based on the model's confidence.
    - Python dtype: `float`
    - Comfy dtype: `FLOAT`
- **`bbox_expansion`**
    - Expands the bounding box of detected segments, providing more context around detected areas.
    - Python dtype: `int`
    - Comfy dtype: `INT`
- **`mask_hint_threshold`**
    - Threshold for applying mask hints, which can refine the detection by focusing on areas of interest or excluding irrelevant areas.
    - Python dtype: `float`
    - Comfy dtype: `FLOAT`
- **`mask_hint_use_negative`**
    - Determines whether to use negative hints in the mask generation process, which can help in excluding certain areas from detection.
    - Python dtype: `str`
    - Comfy dtype: `STRING`
## Output types
- **`mask`**
    - Individual masks for each segment, providing detailed information about the detected areas.
    - Python dtype: `List[torch.Tensor]`
    - Comfy dtype: `MASK`
## Usage tips
- Infra type: `GPU`
- Common nodes: unknown


## Source code
```python
class SAMDetectorSegmented:
    @classmethod
    def INPUT_TYPES(s):
        return {"required": {
                        "sam_model": ("SAM_MODEL", ),
                        "segs": ("SEGS", ),
                        "image": ("IMAGE", ),
                        "detection_hint": (["center-1", "horizontal-2", "vertical-2", "rect-4", "diamond-4", "mask-area",
                                            "mask-points", "mask-point-bbox", "none"],),
                        "dilation": ("INT", {"default": 0, "min": -512, "max": 512, "step": 1}),
                        "threshold": ("FLOAT", {"default": 0.93, "min": 0.0, "max": 1.0, "step": 0.01}),
                        "bbox_expansion": ("INT", {"default": 0, "min": 0, "max": 1000, "step": 1}),
                        "mask_hint_threshold": ("FLOAT", {"default": 0.7, "min": 0.0, "max": 1.0, "step": 0.01}),
                        "mask_hint_use_negative": (["False", "Small", "Outter"], )
                      }
                }

    RETURN_TYPES = ("MASK", "MASK")
    RETURN_NAMES = ("combined_mask", "batch_masks")
    FUNCTION = "doit"

    CATEGORY = "ImpactPack/Detector"

    def doit(self, sam_model, segs, image, detection_hint, dilation,
             threshold, bbox_expansion, mask_hint_threshold, mask_hint_use_negative):
        combined_mask, batch_masks = core.make_sam_mask_segmented(sam_model, segs, image, detection_hint, dilation,
                                                                  threshold, bbox_expansion, mask_hint_threshold,
                                                                  mask_hint_use_negative)
        return (combined_mask, batch_masks, )

```
