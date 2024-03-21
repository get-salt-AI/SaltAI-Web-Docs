# Vec2ToScalarBinaryOperation
## Documentation
- Class name: `CM_Vec2ToScalarBinaryOperation`
- Category: `math/vec2`
- Output node: `False`

Performs a binary operation between two 2D vectors and returns a scalar value. The operation is defined by a set of predefined operations such as dot product or distance calculation.
## Input types
### Required
- **`op`**
    - Specifies the binary operation to perform on the two vectors. It determines the nature of the computation, such as calculating the dot product or the distance between vectors.
    - Python dtype: `str`
    - Comfy dtype: `STRING`
- **`a`**
    - The first 2D vector involved in the binary operation.
    - Python dtype: `tuple[float, float]`
    - Comfy dtype: `VEC2`
- **`b`**
    - The second 2D vector involved in the binary operation.
    - Python dtype: `tuple[float, float]`
    - Comfy dtype: `VEC2`
## Output types
- **`float`**
    - The scalar result of the binary operation performed on the two vectors.
    - Python dtype: `float`
    - Comfy dtype: `FLOAT`
## Usage tips
- Infra type: `GPU`
- Common nodes: unknown

Often used in mathematical and geometric computations within the pipeline, the CM_Vec2ToScalarBinaryOperation node performs a predefined binary operation, such as calculating the dot product or the distance, between two 2D vectors (inputs a and b) and outputs a scalar value.
## Source code
```python
class Vec2ToScalarBinaryOperation:
    @classmethod
    def INPUT_TYPES(cls) -> Mapping[str, Any]:
        return {
            "required": {
                "op": (list(VEC_TO_SCALAR_BINARY_OPERATION.keys()),),
                "a": DEFAULT_VEC2,
                "b": DEFAULT_VEC2,
            }
        }

    RETURN_TYPES = ("FLOAT",)
    FUNCTION = "op"
    CATEGORY = "math/vec2"

    def op(self, op: str, a: Vec2, b: Vec2) -> tuple[float]:
        return (VEC_TO_SCALAR_BINARY_OPERATION[op](numpy.array(a), numpy.array(b)),)

```
