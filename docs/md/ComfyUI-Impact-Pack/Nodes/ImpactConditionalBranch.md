# ImpactConditionalBranch
## Documentation
- Class name: `ImpactConditionalBranch`
- Category: `ImpactPack/Logic`
- Output node: `False`

Executes conditional logic by returning one of two provided values based on the condition. If the condition is true, it returns the first value; otherwise, it returns the second value.
## Input types
### Required
- **`cond`**
    - The condition to evaluate. Determines which of the two values will be returned based on its truthiness.
    - Python dtype: `bool`
    - Comfy dtype: `BOOLEAN`
- **`tt_value`**
    - The value to return if the condition is true. Its type can be any, making this node versatile in conditional operations.
    - Python dtype: `typing.Any`
    - Comfy dtype: `*`
- **`ff_value`**
    - The value to return if the condition is false. Like 'tt_value', its type can be any, allowing for flexibility in conditional logic.
    - Python dtype: `typing.Any`
    - Comfy dtype: `*`
## Output types
- **`*`**
    - Returns either 'tt_value' or 'ff_value' based on the evaluation of 'cond'. The output type matches the type of the returned value.
    - Python dtype: `typing.Any`
    - Comfy dtype: `*`
## Usage tips
- Infra type: `CPU`
- Common nodes: unknown


## Source code
```python
class ImpactConditionalBranch:
    @classmethod
    def INPUT_TYPES(cls):
        return {
            "required": {
                "cond": ("BOOLEAN",),
                "tt_value": (any_typ,),
                "ff_value": (any_typ,),
            },
        }

    FUNCTION = "doit"
    CATEGORY = "ImpactPack/Logic"

    RETURN_TYPES = (any_typ, )

    def doit(self, cond, tt_value, ff_value):
        if cond:
            return (tt_value,)
        else:
            return (ff_value,)

```
