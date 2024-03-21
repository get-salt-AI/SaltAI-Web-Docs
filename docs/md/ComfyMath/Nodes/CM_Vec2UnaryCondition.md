# Vec2UnaryCondition
## Documentation
- Class name: `CM_Vec2UnaryCondition`
- Category: `math/vec2`
- Output node: `False`

Performs a unary condition operation on a 2-dimensional vector. It evaluates a condition specified by the operation on the vector and returns a boolean result.
## Input types
### Required
- **`op`**
    - Specifies the unary condition operation to be performed on the vector. The choice of operation determines how the vector is evaluated to produce a boolean result.
    - Python dtype: `str`
    - Comfy dtype: `STRING`
- **`a`**
    - The 2-dimensional vector on which the unary condition operation is performed. It serves as the input vector for the condition evaluation.
    - Python dtype: `Vec2`
    - Comfy dtype: `VEC2`
## Output types
- **`bool`**
    - The result of the unary condition operation, indicating whether the condition is met for the input vector.
    - Python dtype: `bool`
    - Comfy dtype: `BOOL`
## Usage tips
- Infra type: `CPU`
- Common nodes: unknown

Often used in mathematical and logical operations within the pipeline to evaluate if a 2-dimensional vector meets a specific condition, resulting in a boolean output that can influence subsequent actions or decisions.
## Source code
```python
class Vec2UnaryCondition:
    @classmethod
    def INPUT_TYPES(cls) -> Mapping[str, Any]:
        return {
            "required": {
                "op": (list(VEC_UNARY_CONDITIONS.keys()),),
                "a": DEFAULT_VEC2,
            }
        }

    RETURN_TYPES = ("BOOL",)
    FUNCTION = "op"
    CATEGORY = "math/vec2"

    def op(self, op: str, a: Vec2) -> tuple[bool]:
        return (VEC_UNARY_CONDITIONS[op](numpy.array(a)),)

```
