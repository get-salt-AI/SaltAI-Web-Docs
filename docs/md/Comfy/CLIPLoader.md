# Load CLIP
## Documentation
- Class name: `CLIPLoader`
- Category: `advanced/loaders`
- Output node: `False`

The CLIPLoader node is responsible for loading a CLIP model based on the specified name and type. It supports loading different types of CLIP models, such as 'stable_diffusion' and 'stable_cascade', by fetching the model from a specified path and applying the appropriate CLIP type configuration.
## Input types
### Required
- **`clip_name`**
    - Specifies the name of the CLIP model to be loaded. This name is used to locate the model file in a predefined directory structure.
    - Python dtype: `str`
    - Comfy dtype: `STRING`
- **`type`**
    - Determines the type of CLIP model to load, allowing for different configurations such as 'stable_diffusion' or 'stable_cascade'. This affects how the model is initialized and configured.
    - Python dtype: `str`
    - Comfy dtype: `STRING`
## Output types
- **`clip`**
    - The loaded CLIP model, ready for use in further processing or analysis.
    - Python dtype: `comfy.sd.CLIPModel`
    - Comfy dtype: `CLIP`
## Usage tips
- Infra type: `GPU`
- Common nodes: unknown


## Source code
```python
class CLIPLoader:
    @classmethod
    def INPUT_TYPES(s):
        return {"required": { "clip_name": (folder_paths.get_filename_list("clip"), ),
                              "type": (["stable_diffusion", "stable_cascade"], ),
                             }}
    RETURN_TYPES = ("CLIP",)
    FUNCTION = "load_clip"

    CATEGORY = "advanced/loaders"

    def load_clip(self, clip_name, type="stable_diffusion"):
        clip_type = comfy.sd.CLIPType.STABLE_DIFFUSION
        if type == "stable_cascade":
            clip_type = comfy.sd.CLIPType.STABLE_CASCADE

        clip_path = folder_paths.get_full_path("clip", clip_name)
        clip = comfy.sd.load_clip(ckpt_paths=[clip_path], embedding_directory=folder_paths.get_folder_paths("embeddings"), clip_type=clip_type)
        return (clip,)

```
