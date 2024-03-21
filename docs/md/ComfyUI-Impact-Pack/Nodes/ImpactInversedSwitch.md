# Inversed Switch (Any)
## Documentation
- Class name: `ImpactInversedSwitch`
- Category: `ImpactPack/Util`
- Output node: `False`

This node inverses the selection logic based on the input provided. If the input is a list with two elements, it retrieves a value from a specified node; otherwise, it directly uses the provided input value.
## Input types
### Required
- **`select`**
    - Determines the logic for inversion. If it's a list with two elements, it indicates a dynamic selection based on another node's output; otherwise, it's a static value.
    - Python dtype: `Union[List[Any], Any]`
    - Comfy dtype: `INT, *`
- **`input`**
    - unknown
    - Python dtype: `unknown`
    - Comfy dtype: `*`
## Output types
- **`*`**
    - Holds the inversed selection logic result, either retrieved from another node's output or directly from the input.
    - Python dtype: `Dict[str, Any]`
    - Comfy dtype: `*`
## Usage tips
- Infra type: `CPU`
- Common nodes: unknown


## Source code
```python
class GeneralInversedSwitch:
    @classmethod
    def INPUT_TYPES(s):
        return {"required": {
                    "select": ("INT", {"default": 1, "min": 1, "max": 999999, "step": 1}),
                    "input": (any_typ,),
                    },
                "hidden": {"unique_id": "UNIQUE_ID"},
                }

    RETURN_TYPES = ByPassTypeTuple((any_typ, ))
    FUNCTION = "doit"

    CATEGORY = "ImpactPack/Util"

    def doit(self, select, input, unique_id):
        res = []

        for i in range(0, select):
            if select == i+1:
                res.append(input)
            else:
                res.append(None)

        return res

```
