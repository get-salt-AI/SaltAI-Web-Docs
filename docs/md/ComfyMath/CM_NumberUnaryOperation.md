# NumberUnaryOperation
## Documentation
- Class name: `CM_NumberUnaryOperation`
- Category: `math/number`
- Output node: `False`

Performs a unary operation on a number, where the operation is one of the predefined operations. This method applies the specified operation to the input number and returns the result as a number.
## Input types
### Required
- **`op`**
    - Specifies the operation to be performed on the input number, influencing the computation result.
    - Python dtype: `str`
    - Comfy dtype: `STRING`
- **`a`**
    - The input number on which the unary operation will be performed. It can be an integer or a float, and it significantly influences the outcome of the operation.
    - Python dtype: `Union[int, float]`
    - Comfy dtype: `NUMBER`
## Output types
- **`number`**
    - The result of applying the specified unary operation on the input number, returned as a number.
    - Python dtype: `float`
    - Comfy dtype: `NUMBER`
## Usage tips
- Infra type: `CPU`
- Common nodes: unknown

The CM_NumberUnaryOperation node is primarily utilized for performing a specified unary mathematical operation on a single input number, such as negation or reciprocal, and outputs the result as a number. It is often used in conjunction with nodes requiring mathematical transformations or adjustments to numerical inputs for further processing or analysis.
## Source code
```python
class NumberUnaryOperation:
    @classmethod
    def INPUT_TYPES(cls) -> Mapping[str, Any]:
        return {
            "required": {
                "op": (list(FLOAT_UNARY_OPERATIONS.keys()),),
                "a": DEFAULT_NUMBER,
            }
        }

    RETURN_TYPES = ("NUMBER",)
    FUNCTION = "op"
    CATEGORY = "math/number"

    def op(self, op: str, a: number) -> tuple[float]:
        return (FLOAT_UNARY_OPERATIONS[op](float(a)),)

```
