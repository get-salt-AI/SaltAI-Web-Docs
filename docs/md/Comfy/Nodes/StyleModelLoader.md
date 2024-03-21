# Load Style Model
## Documentation
- Class name: `StyleModelLoader`
- Category: `loaders`
- Output node: `False`

This node is responsible for loading a style model from a specified file. It retrieves the style model's path based on the given name, loads the model, and returns it. This process involves checking the model data for a specific key to determine the correct model configuration before initializing and returning the style model instance.
## Input types
### Required
- **`style_model_name`**
    - Specifies the name of the style model to be loaded. This name is used to retrieve the full path of the style model file from a predefined list of available style models.
    - Python dtype: `str`
    - Comfy dtype: `STRING`
## Output types
- **`style_model`**
    - The loaded style model instance, ready to be used for applying styles to inputs.
    - Python dtype: `StyleModel`
    - Comfy dtype: `STYLE_MODEL`
## Usage tips
- Infra type: `CPU`
- Common nodes: unknown


## Source code
```python
class StyleModelLoader:
    @classmethod
    def INPUT_TYPES(s):
        return {"required": { "style_model_name": (folder_paths.get_filename_list("style_models"), )}}

    RETURN_TYPES = ("STYLE_MODEL",)
    FUNCTION = "load_style_model"

    CATEGORY = "loaders"

    def load_style_model(self, style_model_name):
        style_model_path = folder_paths.get_full_path("style_models", style_model_name)
        style_model = comfy.sd.load_style_model(style_model_path)
        return (style_model,)

```
