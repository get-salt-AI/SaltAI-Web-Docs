# Vec3ScalarOperation
## Documentation
- Class name: `CM_Vec3ScalarOperation`
- Category: `math/vec3`
- Output node: `False`

Performs a scalar operation on a 3-dimensional vector (Vec3) using a specified operation and a scalar value. The operation modifies the vector in a way defined by the operation type, such as multiplication or division by the scalar.
## Input types
### Required
- **`op`**
    - Specifies the type of scalar operation to perform on the vector, such as multiplication or division. The choice of operation affects how the vector's components are modified.
    - Python dtype: `str`
    - Comfy dtype: `STRING`
- **`a`**
    - The 3-dimensional vector (Vec3) to be modified by the scalar operation.
    - Python dtype: `Vec3`
    - Comfy dtype: `VEC3`
- **`b`**
    - The scalar value to be used in the operation. It modifies the vector's components according to the specified operation.
    - Python dtype: `float`
    - Comfy dtype: `FLOAT`
## Output types
- **`vec3`**
    - The modified 3-dimensional vector (Vec3) after applying the specified scalar operation.
    - Python dtype: `Vec3`
    - Comfy dtype: `VEC3`
## Usage tips
- Infra type: `GPU`
- Common nodes: unknown

Often used in mathematical and graphical transformations, the CM_Vec3ScalarOperation node performs a specified scalar operation (such as multiplication or division) on a 3-dimensional vector (Vec3) using a scalar value, modifying the vector's components accordingly. This node is essential for adjusting vector magnitudes or directions in applications involving 3D modeling, physics simulations, or data normalization.
## Source code
```python
class Vec3ScalarOperation:
    @classmethod
    def INPUT_TYPES(cls) -> Mapping[str, Any]:
        return {
            "required": {
                "op": (list(VEC_SCALAR_OPERATION.keys()),),
                "a": DEFAULT_VEC3,
                "b": ("FLOAT",),
            }
        }

    RETURN_TYPES = ("VEC3",)
    FUNCTION = "op"
    CATEGORY = "math/vec3"

    def op(self, op: str, a: Vec3, b: float) -> tuple[Vec3]:
        return (_vec3_from_numpy(VEC_SCALAR_OPERATION[op](numpy.array(a), b)),)

```
