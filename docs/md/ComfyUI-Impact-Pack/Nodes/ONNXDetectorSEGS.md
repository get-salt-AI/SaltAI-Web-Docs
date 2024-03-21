# ONNX Detector (SEGS/legacy) - use BBOXDetector
## Documentation
- Class name: `ONNXDetectorSEGS`
- Category: `ImpactPack/Detector`
- Output node: `False`

This node appears to be a custom implementation for detecting segments in an image using an ONNX model. It likely involves loading a specific ONNX model and applying it to the input image to identify and segment relevant features or objects based on the model's training.
## Input types
### Required
- **`bbox_detector`**
    - unknown
    - Python dtype: `unknown`
    - Comfy dtype: `BBOX_DETECTOR`
- **`image`**
    - The input image on which segment detection is to be performed. The quality and resolution of the image can affect the detection results.
    - Python dtype: `Image`
    - Comfy dtype: `IMAGE`
- **`threshold`**
    - A threshold value to determine the sensitivity of the detection. Higher values may result in fewer but more confident segments, while lower values can detect more segments with less confidence.
    - Python dtype: `float`
    - Comfy dtype: `FLOAT`
- **`dilation`**
    - The amount of dilation applied to the detected segments. This can help in making the segments more pronounced or merging close segments.
    - Python dtype: `int`
    - Comfy dtype: `INT`
- **`crop_factor`**
    - Determines how much of the surrounding area around detected segments is included in the output. A higher crop factor means more surrounding area is included.
    - Python dtype: `float`
    - Comfy dtype: `FLOAT`
- **`drop_size`**
    - Specifies the minimum size for detected segments. Segments smaller than this size are ignored, helping to reduce noise in the detection results.
    - Python dtype: `int`
    - Comfy dtype: `INT`
- **`labels`**
    - unknown
    - Python dtype: `unknown`
    - Comfy dtype: `STRING`
### Optional
- **`detailer_hook`**
    - unknown
    - Python dtype: `unknown`
    - Comfy dtype: `DETAILER_HOOK`
## Output types
- **`segs`**
    - The detected segments in the input image. Each segment is likely represented by its bounding box, mask, or both, depending on the ONNX model's output.
    - Python dtype: `List[Tuple]`
    - Comfy dtype: `SEGS`
## Usage tips
- Infra type: `GPU`
- Common nodes: unknown


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
