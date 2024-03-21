# ImpactFloat
## Documentation
- Class name: `ImpactFloat`
- Category: `ImpactPack/Logic`
- Output node: `False`

The `ImpactFloat` node processes a single floating-point input value and returns it unchanged. This node serves as a pass-through for floating-point values, allowing them to be used or transformed by subsequent nodes in a workflow.
## Input types
### Required
- **`value`**
    - This parameter represents the floating-point value to be processed. It plays a crucial role in the node's operation by being the value that is passed through unchanged.
    - Python dtype: `float`
    - Comfy dtype: `FLOAT`
## Output types
- **`float`**
    - The output is the same floating-point value that was input, demonstrating the node's function as a pass-through for floating-point values.
    - Python dtype: `float`
    - Comfy dtype: `FLOAT`
## Usage tips
- Infra type: `CPU`
- Common nodes: unknown


## Source code
```python
class ImpactFloat:
    @classmethod
    def INPUT_TYPES(cls):
        return {
            "required": {
                "value": ("FLOAT", {"default": 1.0, "min": -3.402823466e+38, "max": 3.402823466e+38}),
            },
        }

    FUNCTION = "doit"
    CATEGORY = "ImpactPack/Logic"

    RETURN_TYPES = ("FLOAT", )

    def doit(self, value):
        return (value, )

```
