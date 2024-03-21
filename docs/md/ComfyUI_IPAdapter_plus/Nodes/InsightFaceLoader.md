# Load InsightFace
## Documentation
- Class name: `InsightFaceLoader`
- Category: `ipadapter`
- Output node: `False`

The `InsightFaceLoader` node is responsible for loading the InsightFace model with a specified provider (e.g., CPU, CUDA). It initializes the model for face analysis tasks, setting up the necessary configurations and preparing it for use.
## Input types
### Required
- **`provider`**
    - Specifies the backend provider for the InsightFace model, which can be CPU, CUDA, or ROCM. The choice of provider is crucial as it directly influences the model's execution speed and efficiency, potentially affecting the overall performance and responsiveness of the face analysis tasks.
    - Python dtype: `str`
    - Comfy dtype: `STRING`
## Output types
- **`insightface`**
    - Returns the initialized InsightFace model, ready for performing face analysis tasks.
    - Python dtype: `tuple[FaceAnalysis]`
    - Comfy dtype: `INSIGHTFACE`
## Usage tips
- Infra type: `GPU`
- Common nodes: `IPAdapterApplyFaceID`

The InsightFaceLoader node is crucial for initializing the InsightFace model for face analysis tasks, particularly in pipelines requiring precise facial recognition and adjustments. It accepts a provider (CPU, CUDA, ROCM) as input and outputs the initialized InsightFace model (`INSIGHTFACE`), ready for performing detailed face analysis in subsequent nodes.
## Source code
```python
class InsightFaceLoader:
    @classmethod
    def INPUT_TYPES(s):
        return {
            "required": {
                "provider": (["CPU", "CUDA", "ROCM"], ),
            },
        }

    RETURN_TYPES = ("INSIGHTFACE",)
    FUNCTION = "load_insight_face"
    CATEGORY = "ipadapter"

    def load_insight_face(self, provider):
        try:
            from insightface.app import FaceAnalysis
        except ImportError as e:
            raise Exception(e)
        
        from insightface.utils import face_align
        global insightface_face_align
        insightface_face_align = face_align

        model = FaceAnalysis(name="buffalo_l", root=INSIGHTFACE_DIR, providers=[provider + 'ExecutionProvider',])
        model.prepare(ctx_id=0, det_size=(640, 640))

        return (model,)

```
