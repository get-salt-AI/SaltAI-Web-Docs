# PhotoMakerLoader
## Documentation
- Class name: `PhotoMakerLoader`
- Category: `_for_testing/photomaker`
- Output node: `False`

This node is responsible for loading a specific PhotoMaker model by its name. It retrieves the model's path, loads the model's state from a file, and optionally extracts a specific component if present, making the model ready for further operations.
## Input types
### Required
- **`photomaker_model_name`**
    - The name of the PhotoMaker model to load. This name is used to locate the model file within a predefined directory structure.
    - Python dtype: `str`
    - Comfy dtype: `STRING`
## Output types
- **`photomaker`**
    - The loaded PhotoMaker model, ready for use in image processing tasks.
    - Python dtype: `PhotoMakerIDEncoder`
    - Comfy dtype: `PHOTOMAKER`
## Usage tips
- Infra type: `CPU`
- Common nodes: unknown


## Source code
```python
class PhotoMakerLoader:
    @classmethod
    def INPUT_TYPES(s):
        return {"required": { "photomaker_model_name": (folder_paths.get_filename_list("photomaker"), )}}

    RETURN_TYPES = ("PHOTOMAKER",)
    FUNCTION = "load_photomaker_model"

    CATEGORY = "_for_testing/photomaker"

    def load_photomaker_model(self, photomaker_model_name):
        photomaker_model_path = folder_paths.get_full_path("photomaker", photomaker_model_name)
        photomaker_model = PhotoMakerIDEncoder()
        data = comfy.utils.load_torch_file(photomaker_model_path, safe_load=True)
        if "id_encoder" in data:
            data = data["id_encoder"]
        photomaker_model.load_state_dict(data)
        return (photomaker_model,)

```
