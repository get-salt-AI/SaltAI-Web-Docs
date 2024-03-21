# ImpactCompare
## Documentation
- Class name: `ImpactCompare`
- Category: `ImpactPack/Logic`
- Output node: `False`

The `ImpactCompare` node performs comparison operations between two input values based on a specified comparison type. It supports basic comparison operations such as equality, inequality, greater than, less than, etc., as well as returning true or false directly. This functionality is crucial for conditional logic within workflows, allowing for dynamic decision-making based on the comparison results.
## Input types
### Required
- **`cmp`**
    - Specifies the type of comparison to perform between the two input values. The choice of comparison determines the logical operation applied, such as equality, inequality, greater than, etc., or directly returning true or false.
    - Python dtype: `List[str]`
    - Comfy dtype: `['STRING']`
- **`a`**
    - The first value to be compared. It can be of any type, making the node versatile for different data types.
    - Python dtype: `Any`
    - Comfy dtype: `ANY`
- **`b`**
    - The second value to be compared. Similar to the first value, it can be of any type, allowing for flexible comparisons.
    - Python dtype: `Any`
    - Comfy dtype: `ANY`
## Output types
- **`boolean`**
    - The result of the comparison operation, indicating whether the specified condition between the two input values is met.
    - Python dtype: `bool`
    - Comfy dtype: `BOOLEAN`
## Usage tips
- Infra type: `CPU`
- Common nodes: unknown


## Source code
```python
class ImpactCompare:
    @classmethod
    def INPUT_TYPES(cls):
        return {
            "required": {
                "cmp": (['a = b', 'a <> b', 'a > b', 'a < b', 'a >= b', 'a <= b', 'tt', 'ff'],),
                "a": (any_typ, ),
                "b": (any_typ, ),
            },
        }

    FUNCTION = "doit"
    CATEGORY = "ImpactPack/Logic"

    RETURN_TYPES = ("BOOLEAN", )

    def doit(self, cmp, a, b):
        if cmp == "a = b":
            return (a == b, )
        elif cmp == "a <> b":
            return (a != b, )
        elif cmp == "a > b":
            return (a > b, )
        elif cmp == "a < b":
            return (a < b, )
        elif cmp == "a >= b":
            return (a >= b, )
        elif cmp == "a <= b":
            return (a <= b, )
        elif cmp == 'tt':
            return (True, )
        else:
            return (False, )

```
