# Load CLIP Vision
## Documentation
- Class name: `CLIPVisionLoader`
- Category: `loaders`
- Output node: `False`

The `CLIPVisionLoader` node is responsible for loading a CLIP Vision model from a specified path. It utilizes the `comfy.clip_vision.load` method to retrieve the model, which is then returned for further use.
## Input types
### Required
- **`clip_name`**
    - The `clip_name` parameter specifies the name of the CLIP Vision model to be loaded. This name is used to locate the model file within a predefined directory structure.
    - Python dtype: `str`
    - Comfy dtype: `STRING`
## Output types
- **`clip_vision`**
    - This output represents the loaded CLIP Vision model, ready for use in subsequent operations such as encoding images.
    - Python dtype: `torch.nn.Module`
    - Comfy dtype: `CLIP_VISION`
## Usage tips
- Infra type: `GPU`
- Common nodes: `IPAdapterApply,IPAdapterApplyFaceID,IPAdapter,IPAdapterEncoder,CLIPVisionEncode,ToIPAdapterPipe //Inspire,Reroute,AV_IPAdapter`


## Source code
```python
class CLIPVisionLoader:
    @classmethod
    def INPUT_TYPES(s):
        return {"required": { "clip_name": (folder_paths.get_filename_list("clip_vision"), ),
                             }}
    RETURN_TYPES = ("CLIP_VISION",)
    FUNCTION = "load_clip"

    CATEGORY = "loaders"

    def load_clip(self, clip_name):
        clip_path = folder_paths.get_full_path("clip_vision", clip_name)
        clip_vision = comfy.clip_vision.load(clip_path)
        return (clip_vision,)

```
