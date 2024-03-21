# BBOX Detector (SEGS)
## Documentation
- Class name: `BboxDetectorSEGS`
- Category: `ImpactPack/Detector`
- Output node: `False`

The BboxDetectorSEGS node is designed to detect bounding boxes in images and further refine these detections into segmentation masks. It leverages a combination of bounding box detection and segmentation techniques, possibly utilizing dilation to enhance the segmentation masks. This node is part of a larger workflow aimed at object detection and segmentation within images, contributing to applications such as image editing, object tracking, and scene understanding.
## Input types
### Required
- **`bbox_detector`**
    - Specifies the bounding box detector model to be used for initial object detection in the image. This model's output serves as the basis for subsequent segmentation.
    - Python dtype: `torch.nn.Module`
    - Comfy dtype: `BBOX_DETECTOR`
- **`image`**
    - The input image on which object detection and segmentation are to be performed. This is the primary data input for the node.
    - Python dtype: `torch.Tensor`
    - Comfy dtype: `IMAGE`
- **`threshold`**
    - A threshold value used to filter detections by confidence. Detections with confidence scores below this threshold are discarded.
    - Python dtype: `float`
    - Comfy dtype: `FLOAT`
- **`dilation`**
    - Specifies the amount of dilation applied to the segmentation masks. Dilation can help in expanding the masks to cover more of the detected objects, improving the segmentation results.
    - Python dtype: `int`
    - Comfy dtype: `INT`
- **`crop_factor`**
    - Determines the factor by which the detected object's bounding box is expanded or contracted for cropping, affecting the size of the segmented area.
    - Python dtype: `float`
    - Comfy dtype: `FLOAT`
- **`drop_size`**
    - Specifies the minimum size for detected objects. Objects smaller than this size are ignored, helping to filter out noise.
    - Python dtype: `int`
    - Comfy dtype: `INT`
- **`labels`**
    - Optional labels to filter the detected objects, allowing for selective segmentation based on object type.
    - Python dtype: `List[str]`
    - Comfy dtype: `STRING`
### Optional
- **`detailer_hook`**
    - An optional hook for custom post-processing of the detected objects, allowing for further refinement of the segmentation results.
    - Python dtype: `Callable`
    - Comfy dtype: `DETAILER_HOOK`
## Output types
- **`segs`**
    - The output segmentation masks that highlight the detected objects within the input image. These masks are the result of combining the bounding box detections with segmentation techniques.
    - Python dtype: `torch.Tensor`
    - Comfy dtype: `SEGS`
## Usage tips
- Infra type: `GPU`
- Common nodes: `DetailerForEach,SAMDetectorCombined,ImpactSegsAndMask,Segs & Mask,ImpactSEGSOrderedFilter`


## Source code
```python
class BboxDetectorForEach:
    @classmethod
    def INPUT_TYPES(s):
        return {"required": {
                        "bbox_detector": ("BBOX_DETECTOR", ),
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

    def doit(self, bbox_detector, image, threshold, dilation, crop_factor, drop_size, labels=None, detailer_hook=None):
        if len(image) > 1:
            raise Exception('[Impact Pack] ERROR: BboxDetectorForEach does not allow image batches.\nPlease refer to https://github.com/ltdrdata/ComfyUI-extension-tutorials/blob/Main/ComfyUI-Impact-Pack/tutorial/batching-detailer.md for more information.')

        segs = bbox_detector.detect(image, threshold, dilation, crop_factor, drop_size, detailer_hook)

        if labels is not None and labels != '':
            labels = labels.split(',')
            if len(labels) > 0:
                segs, _ = segs_nodes.SEGSLabelFilter.filter(segs, labels)

        return (segs, )

```
