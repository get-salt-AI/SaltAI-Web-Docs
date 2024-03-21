# ONNXDetectorProvider
## Documentation
- Class name: `ONNXDetectorProvider`
- Category: `ImpactPack`
- Output node: `False`

The ONNXDetectorProvider class is responsible for loading ONNX models for object detection. It utilizes the `load_onnx` method to load a specified ONNX model from a given path and returns an instance of the ONNXDetector class initialized with the loaded model.
## Input types
### Required
- **`model_name`**
    - Specifies the name of the ONNX model to be loaded. This name is used to retrieve the full path of the model file from a predefined folder structure.
    - Python dtype: `str`
    - Comfy dtype: `STRING`
## Output types
- **`bbox_detector`**
    - Returns an instance of the ONNXDetector class, which is capable of performing object detection on images using the loaded ONNX model.
    - Python dtype: `core.ONNXDetector`
    - Comfy dtype: `BBOX_DETECTOR`
## Usage tips
- Infra type: `GPU`
- Common nodes: unknown


## Source code
```python
class ONNXDetectorProvider:
    @classmethod
    def INPUT_TYPES(s):
        return {"required": {"model_name": (folder_paths.get_filename_list("onnx"), )}}

    RETURN_TYPES = ("BBOX_DETECTOR", )
    FUNCTION = "load_onnx"

    CATEGORY = "ImpactPack"

    def load_onnx(self, model_name):
        model = folder_paths.get_full_path("onnx", model_name)
        return (core.ONNXDetector(model), )

```
