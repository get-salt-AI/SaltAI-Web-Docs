# UltralyticsDetectorProvider
## Documentation
- Class name: `UltralyticsDetectorProvider`
- Category: `ImpactPack`
- Output node: `False`

The provided context does not include a node named UltralyticsDetectorProvider, hence a detailed analysis of its functionality cannot be provided. The documentation should focus on explaining the high-level functionality of a node's method, capturing the essence of what it does without needing to list inputs or outputs explicitly. Since UltralyticsDetectorProvider is not present, such an analysis is not possible.
## Input types
### Required
- **`model_name`**
    - unknown
    - Python dtype: `unknown`
    - Comfy dtype: `[...]`
## Output types
- **`bbox_detector`**
    - unknown
    - Python dtype: `unknown`
    - Comfy dtype: `BBOX_DETECTOR`
- **`segm_detector`**
    - unknown
    - Python dtype: `unknown`
    - Comfy dtype: `SEGM_DETECTOR`
## Usage tips
- Infra type: `CPU`
- Common nodes: `FaceDetailer,BboxDetectorSEGS,ToDetailerPipe,ImpactSimpleDetectorSEGS,Reroute,ImpactSimpleDetectorSEGS_for_AD,SegmDetectorSEGS,ToDetailerPipeSDXL`


## Source code
```python
class UltralyticsDetectorProvider:
    @classmethod
    def INPUT_TYPES(s):
        bboxs = ["bbox/"+x for x in folder_paths.get_filename_list("ultralytics_bbox")]
        segms = ["segm/"+x for x in folder_paths.get_filename_list("ultralytics_segm")]
        return {"required": {"model_name": (bboxs + segms, )}}
    RETURN_TYPES = ("BBOX_DETECTOR", "SEGM_DETECTOR")
    FUNCTION = "doit"

    CATEGORY = "ImpactPack"

    def doit(self, model_name):
        model_path = folder_paths.get_full_path("ultralytics", model_name)
        model = subcore.load_yolo(model_path)

        if model_name.startswith("bbox"):
            return subcore.UltraBBoxDetector(model), core.NO_SEGM_DETECTOR()
        else:
            return subcore.UltraBBoxDetector(model), subcore.UltraSegmDetector(model)

```
