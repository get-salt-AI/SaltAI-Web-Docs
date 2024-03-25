# Vec2BinaryCondition
## Documentation
- Class name: `CM_Vec2BinaryCondition`
- Category: `math/vec2`
- Output node: `False`

This node performs a binary condition operation between two 2-dimensional vectors, evaluating to a boolean result based on the specified operation.
## Input types
### Required
- **`op`**
    - Comfy dtype: `COMBO[STRING]`
    - Specifies the binary condition operation to be applied between the two vectors, determining the nature of the comparison.
    - Python dtype: `str`
- **`a`**
    - Comfy dtype: `VEC2`
    - The first 2-dimensional vector involved in the binary condition operation.
    - Python dtype: `Vec2`
- **`b`**
    - Comfy dtype: `VEC2`
    - The second 2-dimensional vector involved in the binary condition operation.
    - Python dtype: `Vec2`
## Output types
- **`bool`**
    - Comfy dtype: `BOOL`
    - The boolean result of the binary condition operation between the two vectors.
    - Python dtype: `bool`
## Usage tips
- Infra type: `CPU`
- Common nodes: unknown


## Source code
```python
class Vec2BinaryCondition:
    @classmethod
    def INPUT_TYPES(cls) -> Mapping[str, Any]:
        return {
            "required": {
                "op": (list(VEC_BINARY_CONDITIONS.keys()),),
                "a": DEFAULT_VEC2,
                "b": DEFAULT_VEC2,
            }
        }

    RETURN_TYPES = ("BOOL",)
    FUNCTION = "op"
    CATEGORY = "math/vec2"

    def op(self, op: str, a: Vec2, b: Vec2) -> tuple[bool]:
        return (VEC_BINARY_CONDITIONS[op](numpy.array(a), numpy.array(b)),)

```
