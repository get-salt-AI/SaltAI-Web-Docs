# Vec2ScalarOperation
## Documentation
- Class name: `CM_Vec2ScalarOperation`
- Category: `math/vec2`
- Output node: `False`

Performs a scalar operation on a 2D vector. The operation is defined by a set of predefined scalar operations such as multiplication or division, applied between the 2D vector and a scalar value.
## Input types
### Required
- **`op`**
    - Specifies the scalar operation to be performed. It determines how the 2D vector and the scalar value will interact, affecting the result of the operation.
    - Python dtype: `str`
    - Comfy dtype: `STRING`
- **`a`**
    - The 2D vector to which the scalar operation will be applied. It serves as the primary operand in the operation.
    - Python dtype: `Vec2`
    - Comfy dtype: `VEC2`
- **`b`**
    - The scalar value to be used in the operation. It interacts with the 2D vector according to the specified operation.
    - Python dtype: `float`
    - Comfy dtype: `FLOAT`
## Output types
- **`vec2`**
    - The resulting 2D vector after applying the specified scalar operation.
    - Python dtype: `Vec2`
    - Comfy dtype: `VEC2`
## Usage tips
- Infra type: `GPU`
- Common nodes: unknown

Often used in mathematical and graphical computations, the CM_Vec2ScalarOperation node applies a specified scalar operation, such as multiplication or division, between a 2D vector and a scalar value, resulting in a modified 2D vector.
## Source code
```python
class Vec2ScalarOperation:
    @classmethod
    def INPUT_TYPES(cls) -> Mapping[str, Any]:
        return {
            "required": {
                "op": (list(VEC_SCALAR_OPERATION.keys()),),
                "a": DEFAULT_VEC2,
                "b": ("FLOAT",),
            }
        }

    RETURN_TYPES = ("VEC2",)
    FUNCTION = "op"
    CATEGORY = "math/vec2"

    def op(self, op: str, a: Vec2, b: float) -> tuple[Vec2]:
        return (_vec2_from_numpy(VEC_SCALAR_OPERATION[op](numpy.array(a), b)),)

```
