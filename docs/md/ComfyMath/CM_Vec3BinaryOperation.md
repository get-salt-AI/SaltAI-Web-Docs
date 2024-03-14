# Vec3BinaryOperation
## Documentation
- Class name: `CM_Vec3BinaryOperation`
- Category: `math/vec3`
- Output node: `False`

Performs a binary operation on two Vec3 vectors, resulting in a new Vec3 vector. This operation is defined by a specific operation key which dictates the mathematical operation to be applied.
## Input types
### Required
- **`op`**
    - Specifies the binary operation to be applied on the Vec3 vectors. The choice of operation affects the result of the computation.
    - Python dtype: `str`
    - Comfy dtype: `STRING`
- **`a`**
    - The first Vec3 vector to be operated on. It plays a crucial role in the operation as the primary vector.
    - Python dtype: `Vec3`
    - Comfy dtype: `VEC3`
- **`b`**
    - The second Vec3 vector to be operated on. It interacts with the first vector according to the specified operation.
    - Python dtype: `Vec3`
    - Comfy dtype: `VEC3`
## Output types
- **`vec3`**
    - The resulting Vec3 vector after applying the specified binary operation on the input vectors.
    - Python dtype: `Vec3`
    - Comfy dtype: `VEC3`
## Usage tips
- Infra type: `GPU`
- Common nodes: unknown

Often used in mathematical and geometric computations within AI pipelines, the CM_Vec3BinaryOperation node performs a specified binary operation on two Vec3 vectors, resulting in a new Vec3 vector, which is essential for operations like vector addition, subtraction, or cross product in 3D space applications.
## Source code
```python
class Vec3BinaryOperation:
    @classmethod
    def INPUT_TYPES(cls) -> Mapping[str, Any]:
        return {
            "required": {
                "op": (list(VEC_BINARY_OPERATIONS.keys()),),
                "a": DEFAULT_VEC3,
                "b": DEFAULT_VEC3,
            }
        }

    RETURN_TYPES = ("VEC3",)
    FUNCTION = "op"
    CATEGORY = "math/vec3"

    def op(self, op: str, a: Vec3, b: Vec3) -> tuple[Vec3]:
        return (
            _vec3_from_numpy(VEC_BINARY_OPERATIONS[op](numpy.array(a), numpy.array(b))),
        )

```
