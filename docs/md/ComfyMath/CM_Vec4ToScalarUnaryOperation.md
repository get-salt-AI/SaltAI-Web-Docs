# Vec4ToScalarUnaryOperation
## Documentation
- Class name: `CM_Vec4ToScalarUnaryOperation`
- Category: `math/vec4`
- Output node: `False`

Performs a unary operation on a 4-dimensional vector (Vec4) to produce a scalar value. The operation is defined by a set of predefined operations and applied to the vector.
## Input types
### Required
- **`op`**
    - Specifies the unary operation to be applied to the vector. It determines the type of transformation that the vector undergoes to produce a scalar output.
    - Python dtype: `str`
    - Comfy dtype: `STRING`
- **`a`**
    - The 4-dimensional vector (Vec4) to which the unary operation is applied. This vector's values are crucial as they determine the operation's effect and the resulting scalar value, emphasizing the vector's role in shaping the outcome of the operation.
    - Python dtype: `tuple[float, float, float, float]`
    - Comfy dtype: `VEC4`
## Output types
- **`float`**
    - The scalar result of applying the specified unary operation to the input Vec4 vector.
    - Python dtype: `float`
    - Comfy dtype: `FLOAT`
## Usage tips
- Infra type: `GPU`
- Common nodes: unknown

Often used in mathematical and geometric computations where a 4-dimensional vector needs to be simplified to a scalar value, such as calculating the magnitude or applying a custom operation that reduces Vec4 to a single, meaningful figure.
## Source code
```python
class Vec4ToScalarUnaryOperation:
    @classmethod
    def INPUT_TYPES(cls) -> Mapping[str, Any]:
        return {
            "required": {
                "op": (list(VEC_TO_SCALAR_UNARY_OPERATION.keys()),),
                "a": DEFAULT_VEC4,
            }
        }

    RETURN_TYPES = ("FLOAT",)
    FUNCTION = "op"
    CATEGORY = "math/vec4"

    def op(self, op: str, a: Vec4) -> tuple[float]:
        return (VEC_TO_SCALAR_UNARY_OPERATION[op](numpy.array(a)),)

```
