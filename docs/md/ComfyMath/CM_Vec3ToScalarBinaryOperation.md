# Vec3ToScalarBinaryOperation
## Documentation
- Class name: `CM_Vec3ToScalarBinaryOperation`
- Category: `math/vec3`
- Output node: `False`

This node performs a binary operation between two Vec3 vectors, resulting in a scalar value. The operation is defined by a set of predefined operations such as Dot product or Distance calculation.
## Input types
### Required
- **`op`**
    - Specifies the binary operation to be performed on the two Vec3 vectors. It determines the nature of the computation, such as calculating the dot product or the distance between the vectors.
    - Python dtype: `str`
    - Comfy dtype: `STRING`
- **`a`**
    - The first Vec3 vector involved in the binary operation.
    - Python dtype: `Vec3`
    - Comfy dtype: `VEC3`
- **`b`**
    - The second Vec3 vector involved in the binary operation.
    - Python dtype: `Vec3`
    - Comfy dtype: `VEC3`
## Output types
- **`float`**
    - The scalar result of the binary operation between the two Vec3 vectors.
    - Python dtype: `float`
    - Comfy dtype: `FLOAT`
## Usage tips
- Infra type: `GPU`
- Common nodes: unknown

Often used in mathematical and geometric computations within AI pipelines, the CM_Vec3ToScalarBinaryOperation node performs a binary operation, such as calculating the dot product or the distance, between two Vec3 vectors, resulting in a scalar value. This node is crucial for operations requiring the comparison or combination of vector data, such as spatial calculations in 3D environments or physics simulations.
## Source code
```python
class Vec3ToScalarBinaryOperation:
    @classmethod
    def INPUT_TYPES(cls) -> Mapping[str, Any]:
        return {
            "required": {
                "op": (list(VEC_TO_SCALAR_BINARY_OPERATION.keys()),),
                "a": DEFAULT_VEC3,
                "b": DEFAULT_VEC3,
            }
        }

    RETURN_TYPES = ("FLOAT",)
    FUNCTION = "op"
    CATEGORY = "math/vec3"

    def op(self, op: str, a: Vec3, b: Vec3) -> tuple[float]:
        return (VEC_TO_SCALAR_BINARY_OPERATION[op](numpy.array(a), numpy.array(b)),)

```
