# Vec2UnaryOperation
## Documentation
- Class name: `CM_Vec2UnaryOperation`
- Category: `math/vec2`
- Output node: `False`

Performs a unary operation on a 2D vector, transforming it based on a specified operation. This allows for the application of various mathematical operations to modify the vector's properties.
## Input types
### Required
- **`op`**
    - Specifies the unary operation to be applied to the vector. The choice of operation determines how the vector is transformed.
    - Python dtype: `str`
    - Comfy dtype: `STRING`
- **`a`**
    - The 2D vector to be transformed. It serves as the input for the unary operation.
    - Python dtype: `Vec2`
    - Comfy dtype: `VEC2`
## Output types
- **`vec2`**
    - The transformed 2D vector after applying the specified unary operation.
    - Python dtype: `tuple[Vec2]`
    - Comfy dtype: `VEC2`
## Usage tips
- Infra type: `GPU`
- Common nodes: unknown

Often used in vector manipulation tasks to apply mathematical transformations to 2D vectors, such as normalization or rotation, based on the specified unary operation. This node takes a 2D vector as input and outputs the transformed vector, enabling precise control over vector properties in the pipeline.
## Source code
```python
class Vec2UnaryOperation:
    @classmethod
    def INPUT_TYPES(cls) -> Mapping[str, Any]:
        return {
            "required": {
                "op": (list(VEC_UNARY_OPERATIONS.keys()),),
                "a": DEFAULT_VEC2,
            }
        }

    RETURN_TYPES = ("VEC2",)
    FUNCTION = "op"
    CATEGORY = "math/vec2"

    def op(self, op: str, a: Vec2) -> tuple[Vec2]:
        return (_vec2_from_numpy(VEC_UNARY_OPERATIONS[op](numpy.array(a))),)

```
