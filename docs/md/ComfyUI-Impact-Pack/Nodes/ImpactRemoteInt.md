# Remote Int (on prompt)
## Documentation
- Class name: `ImpactRemoteInt`
- Category: `ImpactPack/Logic/_for_test`
- Output node: `True`

The `ImpactRemoteInt` node is designed to remotely set the value of an integer widget within the ComfyUI framework. It targets a specific widget by its name and node ID, updating its value based on the provided integer input.
## Input types
### Required
- **`node_id`**
    - Identifies the target node for the widget value update. It is crucial for ensuring that the correct widget is targeted for the update.
    - Python dtype: `int`
    - Comfy dtype: `INT`
- **`widget_name`**
    - Specifies the name of the widget within the target node to be updated. This ensures that the correct widget is targeted for the value change.
    - Python dtype: `str`
    - Comfy dtype: `STRING`
- **`value`**
    - The integer value to be set for the specified widget. This value updates the widget's current state to reflect the new integer value.
    - Python dtype: `int`
    - Comfy dtype: `INT`
## Output types
The node doesn't have output types
## Usage tips
- Infra type: `CPU`
- Common nodes: unknown


## Source code
```python
class ImpactRemoteInt:
    @classmethod
    def INPUT_TYPES(cls):
        return {"required": {
                    "node_id": ("INT", {"default": 0, "min": 0, "max": 0xffffffffffffffff}),
                    "widget_name": ("STRING", {"multiline": False}),
                    "value": ("INT", {"default": 0, "min": -0xffffffffffffffff, "max": 0xffffffffffffffff}),
                    }}

    FUNCTION = "doit"

    CATEGORY = "ImpactPack/Logic/_for_test"
    RETURN_TYPES = ()
    OUTPUT_NODE = True

    def doit(self, **kwargs):
        return {}

```
