# Vec4ToScalarBinaryOperation
## Documentation
- Class name: `CM_Vec4ToScalarBinaryOperation`
- Category: `math/vec4`
- Output node: `False`

Performs a binary operation between two Vec4 vectors and returns a scalar value. The operation is defined by a set of predefined operations such as dot product or distance calculation.
## Input types
### Required
- **`op`**
    - Specifies the binary operation to perform on the two Vec4 vectors. It determines how the vectors are processed and the nature of the scalar result.
    - Python dtype: `str`
    - Comfy dtype: `STRING`
- **`a`**
    - The first Vec4 vector to be operated on.
    - Python dtype: `Vec4`
    - Comfy dtype: `VEC4`
- **`b`**
    - The second Vec4 vector to be operated on.
    - Python dtype: `Vec4`
    - Comfy dtype: `VEC4`
## Output types
- **`float`**
    - The scalar result of the binary operation performed on the two Vec4 vectors.
    - Python dtype: `float`
    - Comfy dtype: `FLOAT`
## Usage tips
- Infra type: `GPU`
- Common nodes: unknown

Often used in mathematical and geometric operations requiring the comparison or combination of two Vec4 vectors, such as calculating the dot product or the distance between them, to produce a single scalar value as output.
## Source code
```python
class Vec4ToScalarBinaryOperation:
    @classmethod
    def INPUT_TYPES(cls) -> Mapping[str, Any]:
        return {
            "required": {
                "op": (list(VEC_TO_SCALAR_BINARY_OPERATION.keys()),),
                "a": DEFAULT_VEC4,
                "b": DEFAULT_VEC4,
            }
        }

    RETURN_TYPES = ("FLOAT",)
    FUNCTION = "op"
    CATEGORY = "math/vec4"

    def op(self, op: str, a: Vec4, b: Vec4) -> tuple[float]:
        return (VEC_TO_SCALAR_BINARY_OPERATION[op](numpy.array(a), numpy.array(b)),)

```
