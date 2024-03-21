# Switch (Any)
## Documentation
- Class name: `ImpactSwitch`
- Category: `ImpactPack/Util`
- Output node: `False`

The ImpactSwitch node is designed to dynamically select between multiple inputs based on a given condition or index. It is particularly useful in scenarios where the flow of data needs to be conditionally directed, allowing for more flexible and dynamic data processing pipelines within the ImpactPack framework.
## Input types
### Required
- **`select`**
    - This parameter determines which input to select and pass through as the output. Its value dictates the flow of data, making it crucial for conditional logic and dynamic data routing in the node's operation.
    - Python dtype: `int`
    - Comfy dtype: `INT`
- **`sel_mode`**
    - Specifies the mode of selection, enabling different criteria or conditions for selecting the input. This parameter enhances the node's flexibility by allowing various selection strategies.
    - Python dtype: `bool`
    - Comfy dtype: `BOOLEAN`
### Optional
- **`input1`**
    - An optional input parameter that can be dynamically selected based on the 'select' parameter. It adds an additional layer of flexibility, allowing for more complex conditional logic and data routing.
    - Python dtype: `typing.Union[int, str, typing.Any]`
    - Comfy dtype: `*`
## Output types
- **`*`**
    - The output is the data from the selected input, which can vary in type. This dynamic selection mechanism allows for conditional data flow and processing.
    - Python dtype: `typing.Any`
    - Comfy dtype: `*`
- **`string`**
    - Provides a label associated with the selected input, enhancing the interpretability of the output.
    - Python dtype: `str`
    - Comfy dtype: `STRING`
- **`int`**
    - Indicates the index of the selected input, offering insight into the selection process.
    - Python dtype: `int`
    - Comfy dtype: `INT`
## Usage tips
- Infra type: `CPU`
- Common nodes: `EmptyLatentImage`


## Source code
```python
class GeneralSwitch:
    @classmethod
    def INPUT_TYPES(s):
        return {"required": {
                    "select": ("INT", {"default": 1, "min": 1, "max": 999999, "step": 1}),
                    "sel_mode": ("BOOLEAN", {"default": True, "label_on": "select_on_prompt", "label_off": "select_on_execution", "forceInput": False}),
                    },
                "optional": {
                    "input1": (any_typ,),
                    },
                "hidden": {"unique_id": "UNIQUE_ID", "extra_pnginfo": "EXTRA_PNGINFO"}
                }

    RETURN_TYPES = (any_typ, "STRING", "INT")
    RETURN_NAMES = ("selected_value", "selected_label", "selected_index")
    FUNCTION = "doit"

    CATEGORY = "ImpactPack/Util"

    def doit(self, *args, **kwargs):
        selected_index = int(kwargs['select'])
        input_name = f"input{selected_index}"

        selected_label = input_name
        node_id = kwargs['unique_id']
        nodelist = kwargs['extra_pnginfo']['workflow']['nodes']
        for node in nodelist:
            if str(node['id']) == node_id:
                inputs = node['inputs']

                for slot in inputs:
                    if slot['name'] == input_name and 'label' in slot:
                        selected_label = slot['label']

                break

        if input_name in kwargs:
            return (kwargs[input_name], selected_label, selected_index)
        else:
            print(f"ImpactSwitch: invalid select index (ignored)")
            return (None, "", selected_index)

```
