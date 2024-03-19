# Load Upscale Model
## Documentation
- Class name: `UpscaleModelLoader`
- Category: `loaders`
- Output node: `False`

This node is responsible for loading an upscale model by name. It retrieves the model's state dictionary from a specified path, optionally adjusts the state dictionary if necessary, and returns the model in evaluation mode.
## Input types
### Required
- **`model_name`**
    - Specifies the name of the upscale model to load. The available model names are retrieved from a predefined list of upscale models, allowing the user to select from a variety of upscale models tailored to different upscaling needs.
    - Python dtype: `str`
    - Comfy dtype: `STRING`
## Output types
- **`upscale_model`**
    - The loaded upscale model, ready for use in evaluation mode.
    - Python dtype: `torch.nn.Module`
    - Comfy dtype: `UPSCALE_MODEL`
## Usage tips
- Infra type: `CPU`
- Common nodes: `ImageUpscaleWithModel,UltimateSDUpscale,Reroute,LatentPixelScale,Anything Everywhere,PixelKSampleUpscalerProvider`


## Source code
```python
class UpscaleModelLoader:
    @classmethod
    def INPUT_TYPES(s):
        return {"required": { "model_name": (folder_paths.get_filename_list("upscale_models"), ),
                             }}
    RETURN_TYPES = ("UPSCALE_MODEL",)
    FUNCTION = "load_model"

    CATEGORY = "loaders"

    def load_model(self, model_name):
        model_path = folder_paths.get_full_path("upscale_models", model_name)
        sd = comfy.utils.load_torch_file(model_path, safe_load=True)
        if "module.layers.0.residual_group.blocks.0.norm1.weight" in sd:
            sd = comfy.utils.state_dict_prefix_replace(sd, {"module.":""})
        out = model_loading.load_state_dict(sd).eval()
        return (out, )

```
