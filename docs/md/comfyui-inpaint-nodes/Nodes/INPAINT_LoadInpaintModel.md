---
tags:
- DepthMap
- Image
- Inpaint
---

# Load Inpaint Model
## Documentation
- Class name: `INPAINT_LoadInpaintModel`
- Category: `inpaint`
- Output node: `False`

This node is designed to load an inpainting model based on the provided model name. It supports loading models stored in specific formats and prepares them for inpainting tasks by evaluating and configuring them accordingly.
## Input types
### Required
- **`model_name`**
    - The name of the model to be loaded for inpainting. This parameter is crucial for identifying and retrieving the correct model file from a predefined directory structure.
    - Comfy dtype: `COMBO[STRING]`
    - Python dtype: `str`
## Output types
- **`inpaint_model`**
    - Comfy dtype: `INPAINT_MODEL`
    - The loaded and configured inpainting model ready for use in inpainting tasks.
    - Python dtype: `torch.nn.Module`
## Usage tips
- Infra type: `CPU`
- Common nodes: unknown


## Source code
```python
class LoadInpaintModel:
    @classmethod
    def INPUT_TYPES(s):
        return {
            "required": {
                "model_name": (folder_paths.get_filename_list("inpaint"),),
            }
        }

    RETURN_TYPES = ("INPAINT_MODEL",)
    CATEGORY = "inpaint"
    FUNCTION = "load"

    def load(self, model_name: str):
        from spandrel import ModelLoader

        model_file = folder_paths.get_full_path("inpaint", model_name)
        if model_file is None:
            raise RuntimeError(f"Model file not found: {model_name}")
        if model_file.endswith(".pt"):
            sd = torch.jit.load(model_file, map_location="cpu").state_dict()
        else:
            sd = comfy.utils.load_torch_file(model_file, safe_load=True)

        if "synthesis.first_stage.conv_first.conv.resample_filter" in sd:  # MAT
            model = mat.load(sd)
        else:
            model = ModelLoader().load_from_state_dict(sd)
        model = model.eval()
        return (model,)

```
