# GroundingDinoModelLoader (segment anything)
## Documentation
- Class name: `GroundingDinoModelLoader`
- Category: `segment_anything`
- Output node: `False`

This node is responsible for loading a specific GroundingDINO model based on the provided model name. GroundingDINO is a model architecture designed for visual grounding tasks, which involves identifying and localizing objects within images based on textual descriptions. The node dynamically loads the model configuration and weights, adjusts the text encoder if necessary, and prepares the model for inference.
## Input types
### Required
- **`model_name`**
    - Specifies the name of the GroundingDINO model to be loaded. This name is used to retrieve the model's configuration and weights for initialization. The choice of model directly influences the performance and capabilities of the visual grounding task. The model name must be one of the keys from the groundingdino_model_list, which includes a predefined list of available models.
    - Python dtype: `str`
    - Comfy dtype: `STRING`
## Output types
- **`grounding_dino_model`**
    - The loaded GroundingDINO model, ready for performing visual grounding tasks. This model is capable of understanding textual descriptions and localizing the described objects within images.
    - Python dtype: `torch.nn.Module`
    - Comfy dtype: `GROUNDING_DINO_MODEL`
## Usage tips
- Infra type: `GPU`
- Common nodes: `GroundingDinoSAMSegment (segment anything)`

The GroundingDinoModelLoader node is responsible for loading the GroundingDINO model, which excels in visual grounding tasks by identifying and localizing objects within images based on textual descriptions. It is often used with segmentation nodes like GroundingDinoSAMSegment to precisely segment specific parts of the image, enhancing tasks such as image editing, enhancement, and object isolation.
## Source code
```python
class GroundingDinoModelLoader:
    @classmethod
    def INPUT_TYPES(cls):
        return {
            "required": {
                "model_name": (list_groundingdino_model(), ),
            }
        }
    CATEGORY = "segment_anything"
    FUNCTION = "main"
    RETURN_TYPES = ("GROUNDING_DINO_MODEL", )

    def main(self, model_name):
        dino_model = load_groundingdino_model(model_name)
        return (dino_model, )

```
