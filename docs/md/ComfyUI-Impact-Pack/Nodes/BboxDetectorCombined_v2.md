# BBOX Detector (combined)
## Documentation
- Class name: `BboxDetectorCombined_v2`
- Category: `ImpactPack/Detector`
- Output node: `False`

The `BboxDetectorCombined_v2` node is designed to perform object detection by combining the capabilities of a bounding box detector with image processing techniques. It takes an image as input, applies a bounding box detection algorithm to identify objects within the image, and then processes these detections to generate a single combined mask that represents the detected objects. This process involves thresholding to filter detections based on confidence, and optionally dilating the resulting masks to enhance or expand the detected areas.
## Input types
### Required
- **`bbox_detector`**
    - Specifies the bounding box detector model to be used for object detection. This model is crucial as it determines the accuracy and efficiency of the detection process.
    - Python dtype: `torch.nn.Module`
    - Comfy dtype: `BBOX_DETECTOR`
- **`image`**
    - The input image on which object detection and mask generation are to be performed. The quality and resolution of the image can significantly affect the detection results.
    - Python dtype: `torch.Tensor`
    - Comfy dtype: `IMAGE`
- **`threshold`**
    - A confidence threshold for filtering detected objects. Only detections with confidence scores above this threshold will be considered, allowing for the exclusion of less certain detections.
    - Python dtype: `float`
    - Comfy dtype: `FLOAT`
- **`dilation`**
    - The amount of dilation applied to the masks of detected objects. Dilation can help to expand the masks, potentially improving the coverage of detected objects.
    - Python dtype: `int`
    - Comfy dtype: `INT`
## Output types
- **`mask`**
    - The combined mask generated from the detected objects. This mask represents the areas of the image where objects were detected, after applying thresholding and dilation.
    - Python dtype: `torch.Tensor`
    - Comfy dtype: `MASK`
## Usage tips
- Infra type: `GPU`
- Common nodes: unknown


## Source code
```python
class BboxDetectorCombined(SegmDetectorCombined):
    @classmethod
    def INPUT_TYPES(s):
        return {"required": {
                        "bbox_detector": ("BBOX_DETECTOR", ),
                        "image": ("IMAGE", ),
                        "threshold": ("FLOAT", {"default": 0.5, "min": 0.0, "max": 1.0, "step": 0.01}),
                        "dilation": ("INT", {"default": 4, "min": -512, "max": 512, "step": 1}),
                      }
                }

    def doit(self, bbox_detector, image, threshold, dilation):
        mask = bbox_detector.detect_combined(image, threshold, dilation)
        return (mask,)

```
