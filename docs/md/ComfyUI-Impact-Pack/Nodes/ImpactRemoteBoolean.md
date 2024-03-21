# Remote Boolean (on prompt)
## Documentation
- Class name: `ImpactRemoteBoolean`
- Category: `ImpactPack/Logic/_for_test`
- Output node: `True`

The `ImpactRemoteBoolean` node is designed to handle boolean inputs for widgets associated with a specific node in a user interface. It allows for the dynamic updating of widget values based on boolean inputs, facilitating interactive and responsive UI elements within the ComfyUI framework.
## Input types
### Required
- **`node_id`**
    - Identifies the specific node the widget is associated with, enabling targeted updates to the UI based on the input provided.
    - Python dtype: `int`
    - Comfy dtype: `INT`
- **`widget_name`**
    - Specifies the name of the widget to be updated, allowing for precise control over which UI element is affected by the boolean input.
    - Python dtype: `str`
    - Comfy dtype: `STRING`
- **`value`**
    - The boolean input value to be sent to the specified widget, enabling dynamic updates to the UI based on user interactions or programmatic conditions.
    - Python dtype: `bool`
    - Comfy dtype: `BOOLEAN`
## Output types
The node doesn't have output types
## Usage tips
- Infra type: `CPU`
- Common nodes: unknown


## Source code
```python
class ImpactRemoteBoolean:
    @classmethod
    def INPUT_TYPES(cls):
        return {"required": {
                    "node_id": ("INT", {"default": 0, "min": 0, "max": 0xffffffffffffffff}),
                    "widget_name": ("STRING", {"multiline": False}),
                    "value": ("BOOLEAN", {"default": True, "label_on": "True", "label_off": "False"}),
                    }}

    FUNCTION = "doit"

    CATEGORY = "ImpactPack/Logic/_for_test"
    RETURN_TYPES = ()
    OUTPUT_NODE = True

    def doit(self, **kwargs):
        return {}

```
