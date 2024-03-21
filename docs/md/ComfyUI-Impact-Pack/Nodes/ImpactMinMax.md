# ImpactMinMax
## Documentation
- Class name: `ImpactMinMax`
- Category: `ImpactPack/Logic/_for_test`
- Output node: `False`

The `ImpactMinMax` node performs a conditional comparison between two input values based on a mode. If the mode is set to true, it returns the maximum of the two values; otherwise, it returns the minimum. This functionality allows for dynamic selection between two values based on a condition, useful in scenarios where decision-making between two options is required based on a boolean flag.
## Input types
### Required
- **`mode`**
    - Determines the operation mode of the node. If true, the maximum of the two inputs is returned; if false, the minimum is returned. This boolean flag effectively toggles between a 'max' and 'min' operation, allowing for conditional logic in processing the inputs.
    - Python dtype: `bool`
    - Comfy dtype: `BOOLEAN`
- **`a`**
    - One of the two values to be compared. This input, along with 'b', is subject to either a maximum or minimum operation based on the 'mode' flag, influencing the node's output.
    - Python dtype: `typing.Union[int, float, str, list, dict, tuple]`
    - Comfy dtype: `*`
- **`b`**
    - The second of the two values to be compared. Together with 'a', it is evaluated based on the 'mode' to determine the node's output.
    - Python dtype: `typing.Union[int, float, str, list, dict, tuple]`
    - Comfy dtype: `*`
## Output types
- **`int`**
    - The result of the comparison, either the maximum or minimum of the two input values based on the 'mode'. This output is useful for conditional decision-making scenarios.
    - Python dtype: `int`
    - Comfy dtype: `INT`
## Usage tips
- Infra type: `CPU`
- Common nodes: unknown


## Source code
```python
class ImpactMinMax:
    @classmethod
    def INPUT_TYPES(cls):
        return {"required": {
                    "mode": ("BOOLEAN", {"default": True, "label_on": "max", "label_off": "min"}),
                    "a": (any_typ,),
                    "b": (any_typ,),
                    },
                }

    FUNCTION = "doit"

    CATEGORY = "ImpactPack/Logic/_for_test"

    RETURN_TYPES = ("INT", )

    def doit(self, mode, a, b):
        if mode:
            return (max(a, b), )
        else:
            return (min(a, b),)

```
