# ImpactNeg
## Documentation
- Class name: `ImpactNeg`
- Category: `ImpactPack/Logic`
- Output node: `False`

This node inverts the boolean value of its input. It takes a boolean input and returns the opposite boolean value, effectively functioning as a logical NOT operation.
## Input types
### Required
- **`value`**
    - The boolean value to be inverted. This input determines the logical negation operation, directly affecting the node's output.
    - Python dtype: `bool`
    - Comfy dtype: `BOOLEAN`
## Output types
- **`boolean`**
    - The inverted boolean value of the input. This output is the result of applying a logical NOT operation on the input value.
    - Python dtype: `bool`
    - Comfy dtype: `BOOLEAN`
## Usage tips
- Infra type: `CPU`
- Common nodes: unknown


## Source code
```python
class ImpactNeg:
    @classmethod
    def INPUT_TYPES(cls):
        return {
            "required": { "value": ("BOOLEAN", {"forceInput": True}), },
        }

    FUNCTION = "doit"
    CATEGORY = "ImpactPack/Logic"

    RETURN_TYPES = ("BOOLEAN", )

    def doit(self, value):
        return (not value, )

```
