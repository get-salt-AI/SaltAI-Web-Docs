# Vec4UnaryOperation
## Documentation
- Class name: `CM_Vec4UnaryOperation`
- Category: `math/vec4`
- Output node: `False`

Performs a unary operation on a 4-dimensional vector (Vec4) based on a specified operation. This operation transforms the input Vec4 according to the mathematical function associated with the 'op' parameter.
## Input types
### Required
- **`op`**
    - Specifies the unary operation to be performed on the Vec4. The choice of operation affects how the input vector is transformed, directly influencing the node's execution and results by applying a specific mathematical function to the vector.
    - Python dtype: `Enum['Neg', 'Normalize',...]`
    - Comfy dtype: `['Neg', 'Normalize',...]`
- **`a`**
    - The 4-dimensional vector (Vec4) to be transformed by the unary operation.
    - Python dtype: `Vec4`
    - Comfy dtype: `VEC4`
## Output types
- **`vec4`**
    - The transformed 4-dimensional vector (Vec4) after applying the specified unary operation.
    - Python dtype: `Vec4`
    - Comfy dtype: `VEC4`
## Usage tips
- Infra type: `GPU`
- Common nodes: unknown

The CM_Vec4UnaryOperation node is primarily utilized for applying a specified unary operation to a 4-dimensional vector (Vec4), transforming it according to the chosen mathematical function. This node is often used in mathematical and geometric transformations within AI pipelines, taking a Vec4 as input and outputting the transformed Vec4.
## Source code
```python
class Vec4UnaryOperation:
    @classmethod
    def INPUT_TYPES(cls) -> Mapping[str, Any]:
        return {
            "required": {
                "op": (list(VEC_UNARY_OPERATIONS.keys()),),
                "a": DEFAULT_VEC4,
            }
        }

    RETURN_TYPES = ("VEC4",)
    FUNCTION = "op"
    CATEGORY = "math/vec4"

    def op(self, op: str, a: Vec4) -> tuple[Vec4]:
        return (_vec4_from_numpy(VEC_UNARY_OPERATIONS[op](numpy.array(a))),)

```
