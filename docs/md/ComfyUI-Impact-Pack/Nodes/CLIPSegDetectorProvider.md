# CLIPSegDetectorProvider
## Documentation
- Class name: `CLIPSegDetectorProvider`
- Category: `ImpactPack/Util`
- Output node: `False`

The `CLIPSegDetectorProvider` node utilizes the CLIPSeg model to generate bounding box detections based on textual descriptions. It allows for fine-tuning the detection process through parameters such as blur, threshold, and dilation factor, enabling users to adjust the sensitivity and specificity of the detection to their needs.
## Input types
### Required
- **`text`**
    - The textual description that guides the detection process, specifying what objects or features to detect within the image.
    - Python dtype: `str`
    - Comfy dtype: `STRING`
- **`blur`**
    - Specifies the level of blur applied to the image before detection, affecting the smoothness of the detected boundaries.
    - Python dtype: `float`
    - Comfy dtype: `FLOAT`
- **`threshold`**
    - Determines the sensitivity of the detection, with higher values requiring stronger evidence for detection.
    - Python dtype: `float`
    - Comfy dtype: `FLOAT`
- **`dilation_factor`**
    - Controls the extent to which the detected boundaries are expanded or contracted, affecting the size of the detected areas.
    - Python dtype: `int`
    - Comfy dtype: `INT`
## Output types
- **`bbox_detector`**
    - Produces a bounding box detector configured according to the provided textual description and detection parameters.
    - Python dtype: `core.BBoxDetectorBasedOnCLIPSeg`
    - Comfy dtype: `BBOX_DETECTOR`
## Usage tips
- Infra type: `GPU`
- Common nodes: unknown


## Source code
```python
class CLIPSegDetectorProvider:
    @classmethod
    def INPUT_TYPES(s):
        return {"required": {
                        "text": ("STRING", {"multiline": False}),
                        "blur": ("FLOAT", {"min": 0, "max": 15, "step": 0.1, "default": 7}),
                        "threshold": ("FLOAT", {"min": 0, "max": 1, "step": 0.05, "default": 0.4}),
                        "dilation_factor": ("INT", {"min": 0, "max": 10, "step": 1, "default": 4}),
                    }
                }

    RETURN_TYPES = ("BBOX_DETECTOR", )
    FUNCTION = "doit"

    CATEGORY = "ImpactPack/Util"

    def doit(self, text, blur, threshold, dilation_factor):
        if "CLIPSeg" in nodes.NODE_CLASS_MAPPINGS:
            return (core.BBoxDetectorBasedOnCLIPSeg(text, blur, threshold, dilation_factor), )
        else:
            print("[ERROR] CLIPSegToBboxDetector: CLIPSeg custom node isn't installed. You must install biegert/ComfyUI-CLIPSeg extension to use this node.")

```
