# SAMModelLoader (segment anything)
## Documentation
- Class name: `SAMModelLoader (segment anything)`
- Category: `segment_anything`
- Output node: `False`

The SAMModelLoader node is responsible for loading a specific SAM (Segment Anything Model) based on the provided model name. It retrieves the model from a predefined list of available models, downloads it if necessary, and prepares it for inference by setting it to evaluation mode and moving it to the appropriate device (CPU or GPU).
## Input types
### Required
- **`model_name`**
    - Specifies the name of the SAM model to be loaded. This parameter is crucial as it determines which specific model is retrieved and prepared for use, impacting the node's execution and the results of the segmentation process.
    - Python dtype: `str`
    - Comfy dtype: `STRING`
## Output types
- **`sam_model`**
    - The loaded SAM model, ready for inference. This includes setting the model to evaluation mode and moving it to the appropriate device.
    - Python dtype: `torch.nn.Module`
    - Comfy dtype: `SAM_MODEL`
## Usage tips
- Infra type: `GPU`
- Common nodes: `GroundingDinoSAMSegment (segment anything)`

The SAMModelLoader (segment anything) node is responsible for loading a specific SAM (Segment Anything Model) for use in image or video segmentation tasks. It takes a model name as input and outputs a SAM_MODEL, ready for inference, which is crucial for precisely segmenting various objects within the input data without requiring specific training on those objects.
## Source code
```python
class SAMModelLoader:
    @classmethod
    def INPUT_TYPES(cls):
        return {
            "required": {
                "model_name": (list_sam_model(), ),
            }
        }
    CATEGORY = "segment_anything"
    FUNCTION = "main"
    RETURN_TYPES = ("SAM_MODEL", )

    def main(self, model_name):
        sam_model = load_sam_model(model_name)
        return (sam_model, )

```
