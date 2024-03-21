# UNETLoader
## Documentation
- Class name: `UNETLoader`
- Category: `advanced/loaders`
- Output node: `False`

Loads a U-Net model by name from a specified directory. This method is crucial for loading pre-trained U-Net models, which are often used in image segmentation tasks.
## Input types
### Required
- **`unet_name`**
    - Specifies the name of the U-Net model to be loaded. This name is used to locate the model file within a predefined directory structure.
    - Python dtype: `str`
    - Comfy dtype: `STRING`
## Output types
- **`model`**
    - The loaded U-Net model, ready for use in subsequent processing or inference tasks.
    - Python dtype: `torch.nn.Module`
    - Comfy dtype: `MODEL`
## Usage tips
- Infra type: `GPU`
- Common nodes: `PatchModelAddDownscale,Reroute`


## Source code
```python
class UNETLoader:
    @classmethod
    def INPUT_TYPES(s):
        return {"required": { "unet_name": (folder_paths.get_filename_list("unet"), ),
                             }}
    RETURN_TYPES = ("MODEL",)
    FUNCTION = "load_unet"

    CATEGORY = "advanced/loaders"

    def load_unet(self, unet_name):
        unet_path = folder_paths.get_full_path("unet", unet_name)
        model = comfy.sd.load_unet(unet_path)
        return (model,)

```
