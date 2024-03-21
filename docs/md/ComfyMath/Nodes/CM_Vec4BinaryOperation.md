# Vec4BinaryOperation
## Documentation
- Class name: `CM_Vec4BinaryOperation`
- Category: `math/vec4`
- Output node: `False`

Performs a binary operation on two Vec4 vectors, resulting in a new Vec4 vector. The operation is determined by the 'op' parameter, which selects the specific binary operation to apply.
## Input types
### Required
- **`op`**
    - Specifies the binary operation to apply on the Vec4 vectors. It affects the nature of the computation and the resulting vector.
    - Python dtype: `str`
    - Comfy dtype: `STRING`
- **`a`**
    - The first Vec4 vector to be operated on. It serves as one of the inputs to the binary operation.
    - Python dtype: `Vec4`
    - Comfy dtype: `VEC4`
- **`b`**
    - The second Vec4 vector to be operated on. It serves as the other input to the binary operation.
    - Python dtype: `Vec4`
    - Comfy dtype: `VEC4`
## Output types
- **`vec4`**
    - The resulting Vec4 vector after applying the specified binary operation on the input vectors.
    - Python dtype: `Vec4`
    - Comfy dtype: `VEC4`
## Usage tips
- Infra type: `GPU`
- Common nodes: unknown

Often used in mathematical and graphical computations requiring the manipulation of vector data, the CM_Vec4BinaryOperation node performs a specified binary operation on two Vec4 vectors, resulting in a new Vec4 vector as output. This node is essential for operations such as addition, subtraction, or other mathematical transformations on 4-dimensional vectors, making it invaluable in scenarios involving 3D graphics or spatial data analysis.
## Source code
```python
class Vec4BinaryOperation:
    @classmethod
    def INPUT_TYPES(cls) -> Mapping[str, Any]:
        return {
            "required": {
                "op": (list(VEC_BINARY_OPERATIONS.keys()),),
                "a": DEFAULT_VEC4,
                "b": DEFAULT_VEC4,
            }
        }

    RETURN_TYPES = ("VEC4",)
    FUNCTION = "op"
    CATEGORY = "math/vec4"

    def op(self, op: str, a: Vec4, b: Vec4) -> tuple[Vec4]:
        return (
            _vec4_from_numpy(VEC_BINARY_OPERATIONS[op](numpy.array(a), numpy.array(b))),
        )

```
