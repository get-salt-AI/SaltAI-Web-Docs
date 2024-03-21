# ImpactInt
## Documentation
- Class name: `ImpactInt`
- Category: `ImpactPack/Logic`
- Output node: `False`

The `ImpactInt` node is designed to pass through integer values. It serves as a simple utility within the ImpactPack Logic category, allowing for the direct transmission of an integer value without modification.
## Input types
### Required
- **`value`**
    - This parameter represents the integer value to be passed through the node. It is essential for the node's operation as it determines the output of the node.
    - Python dtype: `int`
    - Comfy dtype: `INT`
## Output types
- **`int`**
    - The output is the same integer value that was input, demonstrating the node's pass-through functionality.
    - Python dtype: `int`
    - Comfy dtype: `INT`
## Usage tips
- Infra type: `CPU`
- Common nodes: `ImpactSwitch`


## Source code
```python
class ImpactInt:
    @classmethod
    def INPUT_TYPES(cls):
        return {
            "required": {
                "value": ("INT", {"default": 0, "min": 0, "max": sys.maxsize, "step": 1}),
            },
        }

    FUNCTION = "doit"
    CATEGORY = "ImpactPack/Logic"

    RETURN_TYPES = ("INT", )

    def doit(self, value):
        return (value, )

```
