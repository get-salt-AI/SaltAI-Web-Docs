# Vec3UnaryOperation
## Documentation
- Class name: `CM_Vec3UnaryOperation`
- Category: `math/vec3`
- Output node: `False`

Performs a unary operation on a 3-dimensional vector (Vec3) based on a specified operation. This operation transforms the input Vec3 according to the mathematical function associated with the 'op' parameter.
## Input types
### Required
- **`op`**
    - Specifies the unary operation to perform on the Vec3. The operation is chosen from a predefined list of mathematical functions, allowing for a variety of transformations.
    - Python dtype: `str`
    - Comfy dtype: `STRING`
- **`a`**
    - The 3-dimensional vector (Vec3) to be transformed by the unary operation.
    - Python dtype: `Vec3`
    - Comfy dtype: `VEC3`
## Output types
- **`vec3`**
    - The transformed 3-dimensional vector (Vec3) resulting from the specified unary operation.
    - Python dtype: `Vec3`
    - Comfy dtype: `VEC3`
## Usage tips
- Infra type: `GPU`
- Common nodes: unknown

Often used in mathematical and geometric transformations, the CM_Vec3UnaryOperation node performs a specified unary operation on a 3-dimensional vector (Vec3), transforming it based on the chosen mathematical function. This node takes a Vec3 and an operation type as input and outputs the transformed Vec3.
## Source code
```python
class Vec3UnaryOperation:
    @classmethod
    def INPUT_TYPES(cls) -> Mapping[str, Any]:
        return {
            "required": {
                "op": (list(VEC_UNARY_OPERATIONS.keys()),),
                "a": DEFAULT_VEC3,
            }
        }

    RETURN_TYPES = ("VEC3",)
    FUNCTION = "op"
    CATEGORY = "math/vec3"

    def op(self, op: str, a: Vec3) -> tuple[Vec3]:
        return (_vec3_from_numpy(VEC_UNARY_OPERATIONS[op](numpy.array(a))),)

```
