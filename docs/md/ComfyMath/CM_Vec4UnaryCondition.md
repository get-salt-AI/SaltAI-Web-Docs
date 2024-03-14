# Vec4UnaryCondition
## Documentation
- Class name: `CM_Vec4UnaryCondition`
- Category: `math/vec4`
- Output node: `False`

Performs a unary condition operation on a 4-dimensional vector based on a specified condition. This operation evaluates the vector against the condition and returns a boolean result indicating whether the condition is met.
## Input types
### Required
- **`op`**
    - Specifies the unary condition to be applied to the vector. The choice of operation determines how the vector is evaluated against the condition, directly affecting the outcome by defining the criteria for the condition check.
    - Python dtype: `str`
    - Comfy dtype: `STRING`
- **`a`**
    - The 4-dimensional vector to be evaluated against the specified condition. Its values are crucial as they are directly compared against the condition defined by 'op', influencing whether the condition is met.
    - Python dtype: `Vec4`
    - Comfy dtype: `VEC4`
## Output types
- **`bool`**
    - A boolean value indicating whether the specified condition is met by the vector.
    - Python dtype: `bool`
    - Comfy dtype: `BOOL`
## Usage tips
- Infra type: `GPU`
- Common nodes: unknown

Often used in scenarios requiring logical operations or condition checks on 4-dimensional vectors, such as filtering or decision-making processes based on vector attributes. The CM_Vec4UnaryCondition node takes a 4-dimensional vector and a specified unary condition as input, and outputs a boolean value indicating whether the vector meets the condition.
## Source code
```python
class Vec4UnaryCondition:
    @classmethod
    def INPUT_TYPES(cls) -> Mapping[str, Any]:
        return {
            "required": {
                "op": (list(VEC_UNARY_CONDITIONS.keys()),),
                "a": DEFAULT_VEC4,
            }
        }

    RETURN_TYPES = ("BOOL",)
    FUNCTION = "op"
    CATEGORY = "math/vec4"

    def op(self, op: str, a: Vec4) -> tuple[bool]:
        return (VEC_UNARY_CONDITIONS[op](numpy.array(a)),)

```
