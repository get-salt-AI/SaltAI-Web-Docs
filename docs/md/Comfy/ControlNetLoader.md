# Load ControlNet Model
## Documentation
- Class name: `ControlNetLoader`
- Category: `loaders`
- Output node: `False`

This node is responsible for loading a ControlNet model by name. It retrieves the full path of the specified ControlNet using a helper function and then loads the ControlNet model from that path.
## Input types
### Required
- **`control_net_name`**
    - Specifies the name of the ControlNet model to load. This name is used to retrieve the model's full path for loading. The list of available ControlNet names is dynamically generated based on the contents of a specific directory.
    - Python dtype: `str`
    - Comfy dtype: `STRING`
## Output types
- **`control_net`**
    - The loaded ControlNet model, ready for use in further processing or conditioning steps.
    - Python dtype: `ControlNet`
    - Comfy dtype: `CONTROL_NET`
## Usage tips
- Infra type: `CPU`
- Common nodes: `ControlNetApplyAdvanced,ControlNetApply,ACN_AdvancedControlNetApply,Reroute,ImpactControlNetApplySEGS`


## Source code
```python
class ControlNetLoader:
    @classmethod
    def INPUT_TYPES(s):
        return {"required": { "control_net_name": (folder_paths.get_filename_list("controlnet"), )}}

    RETURN_TYPES = ("CONTROL_NET",)
    FUNCTION = "load_controlnet"

    CATEGORY = "loaders"

    def load_controlnet(self, control_net_name):
        controlnet_path = folder_paths.get_full_path("controlnet", control_net_name)
        controlnet = comfy.controlnet.load_controlnet(controlnet_path)
        return (controlnet,)

```
