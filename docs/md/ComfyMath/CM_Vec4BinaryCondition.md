# Vec4BinaryCondition
## Documentation
- Class name: `CM_Vec4BinaryCondition`
- Category: `math/vec4`
- Output node: `False`

This node performs a binary condition operation on two Vec4 vectors, evaluating a specified condition between them. The operation is determined by the 'op' parameter, which selects the condition from a predefined set of vector binary conditions.
## Input types
### Required
- **`op`**
    - Specifies the binary condition operation to be performed on the vectors. It determines how the two Vec4 vectors will be compared.
    - Python dtype: `str`
    - Comfy dtype: `STRING`
- **`a`**
    - The first Vec4 vector to be compared.
    - Python dtype: `Vec4`
    - Comfy dtype: `VEC4`
- **`b`**
    - The second Vec4 vector to be compared.
    - Python dtype: `Vec4`
    - Comfy dtype: `VEC4`
## Output types
- **`bool`**
    - The result of the binary condition operation, indicating whether the specified condition is true or false between the two Vec4 vectors.
    - Python dtype: `bool`
    - Comfy dtype: `BOOL`
## Usage tips
- Infra type: `CPU`
- Common nodes: unknown

The CM_Vec4BinaryCondition node is utilized for comparing two Vec4 vectors based on a specified binary condition, such as equality or greater than, to output a boolean result indicating whether the condition holds true. This node is often used in mathematical and logical operations within AI pipelines to facilitate decision-making processes based on vector comparisons.
## Source code
```python
class Vec4BinaryCondition:
    @classmethod
    def INPUT_TYPES(cls) -> Mapping[str, Any]:
        return {
            "required": {
                "op": (list(VEC_BINARY_CONDITIONS.keys()),),
                "a": DEFAULT_VEC4,
                "b": DEFAULT_VEC4,
            }
        }

    RETURN_TYPES = ("BOOL",)
    FUNCTION = "op"
    CATEGORY = "math/vec4"

    def op(self, op: str, a: Vec4, b: Vec4) -> tuple[bool]:
        return (VEC_BINARY_CONDITIONS[op](numpy.array(a), numpy.array(b)),)

```
