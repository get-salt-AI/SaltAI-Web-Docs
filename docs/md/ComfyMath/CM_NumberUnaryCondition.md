# NumberUnaryCondition
## Documentation
- Class name: `CM_NumberUnaryCondition`
- Category: `math/number`
- Output node: `False`

This node performs unary operations on numbers based on predefined conditions. It takes a number and applies a specified unary condition operation to it, returning a boolean result indicating whether the condition is met.
## Input types
### Required
- **`op`**
    - Specifies the unary condition operation to be applied to the input number. The choice of operation determines how the input number is evaluated against the condition.
    - Python dtype: `str`
    - Comfy dtype: `STRING`
- **`a`**
    - The input number to which the unary condition operation is applied. The operation's outcome depends on this number's value.
    - Python dtype: `Union[int, float]`
    - Comfy dtype: `NUMBER`
## Output types
- **`bool`**
    - The result of applying the unary condition operation to the input number, indicating whether the condition is met.
    - Python dtype: `bool`
    - Comfy dtype: `BOOL`
## Usage tips
- Infra type: `CPU`
- Common nodes: unknown

The CM_NumberUnaryCondition node is primarily utilized for evaluating numerical values against specific conditions, such as checking if a number is greater than a certain value. It accepts a number and a condition operation as input, and outputs a boolean indicating whether the number meets the specified condition.
## Source code
```python
class NumberUnaryCondition:
    @classmethod
    def INPUT_TYPES(cls) -> Mapping[str, Any]:
        return {
            "required": {
                "op": (list(FLOAT_UNARY_CONDITIONS.keys()),),
                "a": DEFAULT_NUMBER,
            }
        }

    RETURN_TYPES = ("BOOL",)
    FUNCTION = "op"
    CATEGORY = "math/number"

    def op(self, op: str, a: number) -> tuple[bool]:
        return (FLOAT_UNARY_CONDITIONS[op](float(a)),)

```
