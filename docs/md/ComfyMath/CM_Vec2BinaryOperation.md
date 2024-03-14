# Vec2BinaryOperation
## Documentation
- Class name: `CM_Vec2BinaryOperation`
- Category: `math/vec2`
- Output node: `False`

Performs a binary operation on two Vec2 vectors, resulting in a new Vec2 vector. This operation is defined by a specific operation ('op') applied to the input vectors 'a' and 'b'.
## Input types
### Required
- **`op`**
    - Specifies the binary operation to be applied to the vectors. The operation is chosen from a predefined list of vector operations, affecting the result of the node's execution.
    - Python dtype: `str`
    - Comfy dtype: `STRING`
- **`a`**
    - The first input Vec2 vector for the binary operation.
    - Python dtype: `Vec2`
    - Comfy dtype: `VEC2`
- **`b`**
    - The second input Vec2 vector for the binary operation.
    - Python dtype: `Vec2`
    - Comfy dtype: `VEC2`
## Output types
- **`vec2`**
    - The resulting Vec2 vector after applying the specified binary operation on the input vectors.
    - Python dtype: `Vec2`
    - Comfy dtype: `VEC2`
## Usage tips
- Infra type: `CPU`
- Common nodes: unknown

Often used in mathematical and geometric operations requiring the manipulation of vector pairs, the CM_Vec2BinaryOperation node applies a specified binary operation (such as addition, subtraction, etc.) on two input Vec2 vectors 'a' and 'b', resulting in a new Vec2 vector as output.
## Source code
```python
class Vec2BinaryOperation:
    @classmethod
    def INPUT_TYPES(cls) -> Mapping[str, Any]:
        return {
            "required": {
                "op": (list(VEC_BINARY_OPERATIONS.keys()),),
                "a": DEFAULT_VEC2,
                "b": DEFAULT_VEC2,
            }
        }

    RETURN_TYPES = ("VEC2",)
    FUNCTION = "op"
    CATEGORY = "math/vec2"

    def op(self, op: str, a: Vec2, b: Vec2) -> tuple[Vec2]:
        return (
            _vec2_from_numpy(VEC_BINARY_OPERATIONS[op](numpy.array(a), numpy.array(b))),
        )

```
