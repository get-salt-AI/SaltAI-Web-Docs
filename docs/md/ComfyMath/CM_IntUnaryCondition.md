# IntUnaryCondition
## Documentation
- Class name: `CM_IntUnaryCondition`
- Category: `math/int`
- Output node: `False`

This node performs unary operations on integers based on a specified condition. It evaluates a single integer input against a unary condition (e.g., checking if the integer is positive, negative, or zero) and returns a boolean indicating the result of the condition.
## Input types
### Required
- **`op`**
    - Specifies the unary operation to be performed on the integer. The choice of operation determines how the integer is evaluated against the condition.
    - Python dtype: `str`
    - Comfy dtype: `STRING`
- **`a`**
    - The integer to be evaluated against the specified unary condition.
    - Python dtype: `int`
    - Comfy dtype: `INT`
## Output types
- **`bool`**
    - A boolean indicating the result of evaluating the integer against the specified unary condition.
    - Python dtype: `bool`
    - Comfy dtype: `BOOL`
## Usage tips
- Infra type: `CPU`
- Common nodes: unknown

Often used in decision-making processes within the pipeline to evaluate an integer's property, such as checking if it is positive, negative, or zero, and outputs a boolean result based on the specified condition.
## Source code
```python
class IntUnaryCondition:
    @classmethod
    def INPUT_TYPES(cls) -> Mapping[str, Any]:
        return {
            "required": {"op": (list(INT_UNARY_CONDITIONS.keys()),), "a": DEFAULT_INT}
        }

    RETURN_TYPES = ("BOOL",)
    FUNCTION = "op"
    CATEGORY = "math/int"

    def op(self, op: str, a: int) -> tuple[bool]:
        return (INT_UNARY_CONDITIONS[op](a),)

```
