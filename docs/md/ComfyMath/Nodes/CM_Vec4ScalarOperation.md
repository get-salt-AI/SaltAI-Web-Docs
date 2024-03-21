# Vec4ScalarOperation
## Documentation
- Class name: `CM_Vec4ScalarOperation`
- Category: `math/vec4`
- Output node: `False`

Performs a scalar operation on a Vec4 vector, applying a specified operation (e.g., multiplication or division) between the vector and a scalar value, resulting in a transformed Vec4 vector.
## Input types
### Required
- **`op`**
    - Specifies the scalar operation to perform on the Vec4 vector.
    - Python dtype: `str`
    - Comfy dtype: `STRING`
- **`a`**
    - The Vec4 vector to be transformed.
    - Python dtype: `Vec4`
    - Comfy dtype: `VEC4`
- **`b`**
    - The scalar value to be used in the operation.
    - Python dtype: `float`
    - Comfy dtype: `FLOAT`
## Output types
- **`vec4`**
    - The resulting Vec4 vector after the operation.
    - Python dtype: `Vec4`
    - Comfy dtype: `VEC4`
## Usage tips
- Infra type: `CPU`
- Common nodes: unknown

The CM_Vec4ScalarOperation node is primarily utilized for performing mathematical operations on a Vec4 vector by applying a scalar operation (e.g., multiplication or division) between the vector and a scalar value, resulting in a modified Vec4 vector. This node is often used in conjunction with nodes that require vector transformations or adjustments in their processing pipeline, enabling precise control over vector attributes without the need for complex coding.
## Source code
```python
class Vec4ScalarOperation:
    @classmethod
    def INPUT_TYPES(cls) -> Mapping[str, Any]:
        return {
            "required": {
                "op": (list(VEC_SCALAR_OPERATION.keys()),),
                "a": DEFAULT_VEC4,
                "b": ("FLOAT",),
            }
        }

    RETURN_TYPES = ("VEC4",)
    FUNCTION = "op"
    CATEGORY = "math/vec4"

    def op(self, op: str, a: Vec4, b: float) -> tuple[Vec4]:
        return (_vec4_from_numpy(VEC_SCALAR_OPERATION[op](numpy.array(a), b)),)

```
