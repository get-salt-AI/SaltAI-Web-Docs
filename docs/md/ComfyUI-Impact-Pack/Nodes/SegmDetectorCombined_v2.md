# SEGM Detector (combined)
## Documentation
- Class name: `SegmDetectorCombined_v2`
- Category: `ImpactPack/Detector`
- Output node: `False`

This node represents an advanced version of a segmentation detector that combines the functionalities of segmentation and bounding box detection. It is designed to process an image, apply a segmentation model to detect areas of interest, and optionally apply dilation to the detected segments. The node is capable of handling both segmentation and bounding box models, making it versatile for various detection tasks.
## Input types
### Required
- **`segm_detector`**
    - The segmentation detector model used for detecting areas of interest within the image. It plays a crucial role in the overall detection process by identifying segments based on the provided model.
    - Python dtype: `torch.nn.Module`
    - Comfy dtype: `SEGM_DETECTOR`
- **`image`**
    - The input image to be processed. The image is analyzed by the segmentation detector to identify areas of interest.
    - Python dtype: `torch.Tensor`
    - Comfy dtype: `IMAGE`
- **`threshold`**
    - A threshold value used to filter the detection results. Only detections with confidence scores above this threshold are considered.
    - Python dtype: `float`
    - Comfy dtype: `FLOAT`
- **`dilation`**
    - An optional parameter that applies dilation to the detected segments, enhancing their visibility or merging close segments.
    - Python dtype: `int`
    - Comfy dtype: `INT`
## Output types
- **`mask`**
    - The output mask representing the detected areas of interest within the image. It is the result of applying the segmentation model and any specified post-processing steps like dilation.
    - Python dtype: `torch.Tensor`
    - Comfy dtype: `MASK`
## Usage tips
- Infra type: `GPU`
- Common nodes: unknown


## Source code
```python
class SegmDetectorCombined:
    @classmethod
    def INPUT_TYPES(s):
        return {"required": {
                        "segm_detector": ("SEGM_DETECTOR", ),
                        "image": ("IMAGE", ),
                        "threshold": ("FLOAT", {"default": 0.5, "min": 0.0, "max": 1.0, "step": 0.01}),
                        "dilation": ("INT", {"default": 0, "min": -512, "max": 512, "step": 1}),
                      }
                }

    RETURN_TYPES = ("MASK",)
    FUNCTION = "doit"

    CATEGORY = "ImpactPack/Detector"

    def doit(self, segm_detector, image, threshold, dilation):
        mask = segm_detector.detect_combined(image, threshold, dilation)
        return (mask,)

```
