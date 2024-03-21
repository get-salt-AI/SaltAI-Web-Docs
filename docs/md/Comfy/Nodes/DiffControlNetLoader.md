# Load ControlNet Model (diff)
## Documentation
- Class name: `DiffControlNetLoader`
- Category: `loaders`
- Output node: `False`

This node is responsible for loading a differential control network model, which is a specialized model designed to apply modifications or controls to another model based on a given control network name and the model itself. It enhances the flexibility and capability of the control network by allowing it to adapt based on the specific model it is applied to.
## Input types
### Required
- **`model`**
    - The model parameter is crucial as it specifies the base model to which the differential control network will be applied. This allows the control network to adapt its behavior based on the characteristics of the base model, enhancing its effectiveness.
    - Python dtype: `torch.nn.Module`
    - Comfy dtype: `MODEL`
- **`control_net_name`**
    - The control_net_name parameter specifies the name of the control network to be loaded. It is essential for identifying the correct control network file from a list of available control networks, enabling the dynamic loading of different control networks based on requirements.
    - Python dtype: `str`
    - Comfy dtype: `STRING`
## Output types
- **`control_net`**
    - The output is a control network model that has been loaded and potentially adapted based on the input model. This control network can then be applied to modify or control the behavior of the input model in specific ways.
    - Python dtype: `ControlNet`
    - Comfy dtype: `CONTROL_NET`
## Usage tips
- Infra type: `GPU`
- Common nodes: unknown


## Source code
```python
class DiffControlNetLoader:
    @classmethod
    def INPUT_TYPES(s):
        return {"required": { "model": ("MODEL",),
                              "control_net_name": (folder_paths.get_filename_list("controlnet"), )}}

    RETURN_TYPES = ("CONTROL_NET",)
    FUNCTION = "load_controlnet"

    CATEGORY = "loaders"

    def load_controlnet(self, model, control_net_name):
        controlnet_path = folder_paths.get_full_path("controlnet", control_net_name)
        controlnet = comfy.controlnet.load_controlnet(controlnet_path, model)
        return (controlnet,)

```
