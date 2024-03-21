# Set Widget Value
## Documentation
- Class name: `ImpactSetWidgetValue`
- Category: `ImpactPack/Logic/_for_test`
- Output node: `True`

This node is designed to set the value of a widget within a node in a workflow based on various input types (boolean, integer, float, string). It determines the type of the input value and sends a synchronous request to update the widget's value accordingly.
## Input types
### Required
- **`signal`**
    - Acts as a trigger for the node's operation. It doesn't directly influence the widget value but is necessary for the node's execution flow.
    - Python dtype: `Any`
    - Comfy dtype: `*`
- **`node_id`**
    - Identifies the target node whose widget value is to be updated. It is crucial for specifying the exact node in the workflow to be modified.
    - Python dtype: `int`
    - Comfy dtype: `INT`
- **`widget_name`**
    - Specifies the name of the widget within the target node that is to be updated. This is essential for identifying the correct widget to modify.
    - Python dtype: `str`
    - Comfy dtype: `STRING`
### Optional
- **`boolean_value`**
    - The boolean value to set for the widget, if applicable. This input allows for boolean-based widget updates.
    - Python dtype: `Optional[bool]`
    - Comfy dtype: `BOOLEAN`
- **`int_value`**
    - The integer value to set for the widget, if applicable. This input enables numeric widget updates.
    - Python dtype: `Optional[int]`
    - Comfy dtype: `INT`
- **`float_value`**
    - The float value to set for the widget, if applicable. This input facilitates decimal-based widget updates.
    - Python dtype: `Optional[float]`
    - Comfy dtype: `FLOAT`
- **`string_value`**
    - The string value to set for the widget, if applicable. This input allows for text-based widget updates.
    - Python dtype: `Optional[str]`
    - Comfy dtype: `STRING`
## Output types
- **`*`**
    - Returns the input signal, indicating the completion of the widget update operation.
    - Python dtype: `Any`
    - Comfy dtype: `*`
## Usage tips
- Infra type: `CPU`
- Common nodes: unknown


## Source code
```python
class ImpactSetWidgetValue:
    @classmethod
    def INPUT_TYPES(cls):
        return {"required": {
                    "signal": (any_typ,),
                    "node_id": ("INT", {"default": 0, "min": 0, "max": 0xffffffffffffffff}),
                    "widget_name": ("STRING", {"multiline": False}),
                    },
                "optional": {
                    "boolean_value": ("BOOLEAN", {"forceInput": True}),
                    "int_value": ("INT", {"forceInput": True}),
                    "float_value": ("FLOAT", {"forceInput": True}),
                    "string_value": ("STRING", {"forceInput": True}),
                    }
                }

    FUNCTION = "doit"

    CATEGORY = "ImpactPack/Logic/_for_test"
    RETURN_TYPES = (any_typ,)
    RETURN_NAMES = ("signal_opt",)
    OUTPUT_NODE = True

    def doit(self, signal, node_id, widget_name, boolean_value=None, int_value=None, float_value=None, string_value=None, ):
        kind = None
        if boolean_value is not None:
            value = boolean_value
            kind = "BOOLEAN"
        elif int_value is not None:
            value = int_value
            kind = "INT"
        elif float_value is not None:
            value = float_value
            kind = "FLOAT"
        elif string_value is not None:
            value = string_value
            kind = "STRING"
        else:
            value = None

        if value is not None:
            PromptServer.instance.send_sync("impact-node-feedback",
                                            {"node_id": node_id, "widget_name": widget_name, "type": kind, "value": value})

        return (signal,)

```
