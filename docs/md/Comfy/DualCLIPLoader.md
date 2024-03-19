# DualCLIPLoader
## Documentation
- Class name: `DualCLIPLoader`
- Category: `advanced/loaders`
- Output node: `False`

The DualCLIPLoader node is designed to load two CLIP models simultaneously. It takes the names of two CLIP models as input, retrieves their paths, and loads them into a single CLIP model instance with embeddings from a specified directory. This node is useful for operations that require the simultaneous use of two distinct CLIP models.
## Input types
### Required
- **`clip_name1`**
    - Specifies the name of the first CLIP model to be loaded. The name is used to retrieve the model's path for loading.
    - Python dtype: `str`
    - Comfy dtype: `STRING`
- **`clip_name2`**
    - Specifies the name of the second CLIP model to be loaded. Similar to clip_name1, it is used to retrieve the model's path for loading.
    - Python dtype: `str`
    - Comfy dtype: `STRING`
## Output types
- **`clip`**
    - The loaded CLIP model instance, which combines the embeddings and configurations of the two specified CLIP models.
    - Python dtype: `torch.nn.Module`
    - Comfy dtype: `CLIP`
## Usage tips
- Infra type: `GPU`
- Common nodes: unknown


## Source code
```python
class DualCLIPLoader:
    @classmethod
    def INPUT_TYPES(s):
        return {"required": { "clip_name1": (folder_paths.get_filename_list("clip"), ), "clip_name2": (folder_paths.get_filename_list("clip"), ),
                             }}
    RETURN_TYPES = ("CLIP",)
    FUNCTION = "load_clip"

    CATEGORY = "advanced/loaders"

    def load_clip(self, clip_name1, clip_name2):
        clip_path1 = folder_paths.get_full_path("clip", clip_name1)
        clip_path2 = folder_paths.get_full_path("clip", clip_name2)
        clip = comfy.sd.load_clip(ckpt_paths=[clip_path1, clip_path2], embedding_directory=folder_paths.get_folder_paths("embeddings"))
        return (clip,)

```
