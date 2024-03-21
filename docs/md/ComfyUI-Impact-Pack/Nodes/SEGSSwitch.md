# Switch (SEGS/legacy)
## Documentation
- Class name: `SEGSSwitch`
- Category: `ImpactPack/Util`
- Output node: `False`

The SEGSSwitch node dynamically selects one of the input segmentation sets based on the provided index. It's designed to facilitate conditional processing of different segmentation sets within a workflow, enhancing flexibility and control over which data is processed at any given stage.
## Input types
### Required
- **`select`**
    - Specifies the index of the segmentation set to be selected for output. It ensures that the workflow can dynamically choose between multiple segmentation inputs based on conditions or preferences.
    - Python dtype: `int`
    - Comfy dtype: `INT`
- **`sel_mode`**
    - unknown
    - Python dtype: `unknown`
    - Comfy dtype: `BOOLEAN`
### Optional
- **`input1`**
    - unknown
    - Python dtype: `unknown`
    - Comfy dtype: `*`
## Output types
- **`*`**
    - Outputs the selected segmentation set. This allows for conditional processing and routing of segmentation data within a workflow.
    - Python dtype: `Tuple[str, List[SEG]]`
    - Comfy dtype: `*`
- **`string`**
    - unknown
    - Python dtype: `unknown`
    - Comfy dtype: `STRING`
- **`int`**
    - unknown
    - Python dtype: `unknown`
    - Comfy dtype: `INT`
## Usage tips
- Infra type: `CPU`
- Common nodes: unknown


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
