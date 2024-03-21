# SEGM Detector (SEGS)
## Documentation
- Class name: `SegmDetectorSEGS`
- Category: `ImpactPack/Detector`
- Output node: `False`

The `SegmDetectorSEGS` node appears to be a conceptual or misnamed entity within the provided context, as there is no direct reference to a class or method named `SegmDetectorSEGS`. However, based on the naming convention and context, it might be related to segmentation detection tasks, specifically designed to process images, detect segments within them, and possibly return segmented parts of the images along with additional metadata. This analysis would involve applying a segmentation model to an input image, processing the results to extract segments, and potentially applying post-processing steps such as dilation to refine the segments.
## Input types
### Required
- **`segm_detector`**
    - Specifies the segmentation model to be used for detecting segments within the image. The choice of model can significantly affect the accuracy and performance of the segmentation task.
    - Python dtype: `torch.nn.Module`
    - Comfy dtype: `SEGM_DETECTOR`
- **`image`**
    - The input image to be processed. The segmentation model will be applied to this image to detect and segment relevant features or objects.
    - Python dtype: `torch.Tensor`
    - Comfy dtype: `IMAGE`
- **`threshold`**
    - A threshold value used to filter the segmentation results. Segments with confidence scores below this threshold may be discarded, affecting the sensitivity of the detection.
    - Python dtype: `float`
    - Comfy dtype: `FLOAT`
- **`dilation`**
    - An optional post-processing step to dilate the detected segments. This can help in making the segments more pronounced or in merging closely located segments.
    - Python dtype: `int`
    - Comfy dtype: `INT`
- **`crop_factor`**
    - The factor by which the crop region around each detected segment is expanded or contracted, affecting the size of the cropped area around each segment.
    - Python dtype: `float`
    - Comfy dtype: `FLOAT`
- **`drop_size`**
    - The minimum size for detected segments. Segments smaller than this size are discarded, ensuring that only segments of significant size are processed.
    - Python dtype: `int`
    - Comfy dtype: `INT`
- **`labels`**
    - Optional labels used to filter the detected segments. Only segments with matching labels are retained, allowing for targeted segmentation based on specific criteria.
    - Python dtype: `Optional[List[str]]`
    - Comfy dtype: `STRING`
### Optional
- **`detailer_hook`**
    - An optional hook for custom post-processing of detected segments. This allows for additional customization and refinement of the segmentation results.
    - Python dtype: `Optional[Callable]`
    - Comfy dtype: `FUNCTION`
## Output types
- **`segs`**
    - The output consists of segmented parts of the input image, potentially along with additional metadata such as confidence scores and bounding box coordinates.
    - Python dtype: `List[torch.Tensor]`
    - Comfy dtype: `SEGS`
## Usage tips
- Infra type: `GPU`
- Common nodes: `ImpactSEGSOrderedFilter`


## Source code
```python
class SegmDetectorForEach:
    @classmethod
    def INPUT_TYPES(s):
        return {"required": {
                        "segm_detector": ("SEGM_DETECTOR", ),
                        "image": ("IMAGE", ),
                        "threshold": ("FLOAT", {"default": 0.5, "min": 0.0, "max": 1.0, "step": 0.01}),
                        "dilation": ("INT", {"default": 10, "min": -512, "max": 512, "step": 1}),
                        "crop_factor": ("FLOAT", {"default": 3.0, "min": 1.0, "max": 100, "step": 0.1}),
                        "drop_size": ("INT", {"min": 1, "max": MAX_RESOLUTION, "step": 1, "default": 10}),
                        "labels": ("STRING", {"multiline": True, "default": "all", "placeholder": "List the types of segments to be allowed, separated by commas"}),
                      },
                "optional": {"detailer_hook": ("DETAILER_HOOK",), }
                }

    RETURN_TYPES = ("SEGS", )
    FUNCTION = "doit"

    CATEGORY = "ImpactPack/Detector"

    def doit(self, segm_detector, image, threshold, dilation, crop_factor, drop_size, labels=None, detailer_hook=None):
        if len(image) > 1:
            raise Exception('[Impact Pack] ERROR: SegmDetectorForEach does not allow image batches.\nPlease refer to https://github.com/ltdrdata/ComfyUI-extension-tutorials/blob/Main/ComfyUI-Impact-Pack/tutorial/batching-detailer.md for more information.')

        segs = segm_detector.detect(image, threshold, dilation, crop_factor, drop_size, detailer_hook)

        if labels is not None and labels != '':
            labels = labels.split(',')
            if len(labels) > 0:
                segs, _ = segs_nodes.SEGSLabelFilter.filter(segs, labels)

        return (segs, )

```
