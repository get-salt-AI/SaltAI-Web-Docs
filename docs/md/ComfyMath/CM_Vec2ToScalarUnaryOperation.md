# Vec2ToScalarUnaryOperation
## Documentation
- Class name: `CM_Vec2ToScalarUnaryOperation`
- Category: `math/vec2`
- Output node: `False`

Performs a unary operation that transforms a 2D vector into a scalar value based on a specified operation. This operation is defined by a set of predefined functions that take a 2D vector as input and return a scalar value.
## Input types
### Required
- **`op`**
    - Specifies the unary operation to be performed on the 2D vector. The choice of operation significantly influences the transformation process, determining the mathematical manipulation applied to convert the vector into a scalar value.
    - Python dtype: `str`
    - Comfy dtype: `STRING`
- **`a`**
    - The 2D vector on which the unary operation is to be performed. It serves as the input to the chosen operation, and its structure is crucial for the operation's execution.
    - Python dtype: `tuple[float, float]`
    - Comfy dtype: `VEC2`
## Output types
- **`float`**
    - The scalar result of the unary operation performed on the 2D vector.
    - Python dtype: `float`
    - Comfy dtype: `FLOAT`
## Usage tips
- Infra type: `GPU`
- Common nodes: unknown

Often used in mathematical and geometric computations within the pipeline, the CM_Vec2ToScalarUnaryOperation node transforms a 2D vector into a scalar value by applying a specified unary operation, such as calculating the magnitude or performing a custom mathematical function. This node takes a 2D vector as input and outputs a scalar value, making it essential for operations that require dimensionality reduction or specific mathematical transformations.
## Source code
```python
class Vec2ToScalarUnaryOperation:
    @classmethod
    def INPUT_TYPES(cls) -> Mapping[str, Any]:
        return {
            "required": {
                "op": (list(VEC_TO_SCALAR_UNARY_OPERATION.keys()),),
                "a": DEFAULT_VEC2,
            }
        }

    RETURN_TYPES = ("FLOAT",)
    FUNCTION = "op"
    CATEGORY = "math/vec2"

    def op(self, op: str, a: Vec2) -> tuple[float]:
        return (VEC_TO_SCALAR_UNARY_OPERATION[op](numpy.array(a)),)

```
