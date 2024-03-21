# NumberBinaryCondition
## Documentation
- Class name: `CM_NumberBinaryCondition`
- Category: `math/float`
- Output node: `False`

This node performs a binary condition operation on two numbers, evaluating a specified condition between them. It supports a variety of conditions, such as equality, inequality, and comparison operations, determined by the 'op' parameter.
## Input types
### Required
- **`op`**
    - Specifies the binary condition operation to be performed, such as equality or inequality. The choice of operation directly influences the comparison or evaluation process between the two input numbers, impacting the node's execution and the nature of the result.
    - Python dtype: `str`
    - Comfy dtype: `STRING`
- **`a`**
    - The first number to be compared or evaluated in the binary condition operation. Its value plays a crucial role in determining the outcome of the specified condition, affecting the node's execution and results.
    - Python dtype: `float`
    - Comfy dtype: `NUMBER`
- **`b`**
    - The second number to be compared or evaluated, alongside the first number, in the binary condition operation. Its value is essential in evaluating the specified condition, influencing the node's execution and the final outcome.
    - Python dtype: `float`
    - Comfy dtype: `NUMBER`
## Output types
- **`bool`**
    - The result of the binary condition operation, indicating whether the specified condition holds true between the two input numbers.
    - Python dtype: `bool`
    - Comfy dtype: `BOOL`
## Usage tips
- Infra type: `CPU`
- Common nodes: unknown

Often used in decision-making processes within the pipeline to compare two numbers based on a specified condition, such as equality, greater than, or less than, influencing the flow or outcome of the AI application.
## Source code
```python
class NumberBinaryCondition:
    @classmethod
    def INPUT_TYPES(cls) -> Mapping[str, Any]:
        return {
            "required": {
                "op": (list(FLOAT_BINARY_CONDITIONS.keys()),),
                "a": DEFAULT_NUMBER,
                "b": DEFAULT_NUMBER,
            }
        }

    RETURN_TYPES = ("BOOL",)
    FUNCTION = "op"
    CATEGORY = "math/float"

    def op(self, op: str, a: number, b: number) -> tuple[bool]:
        return (FLOAT_BINARY_CONDITIONS[op](float(a), float(b)),)

```
