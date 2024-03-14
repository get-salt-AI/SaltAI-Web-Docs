# IntBinaryCondition
## Documentation
- Class name: `CM_IntBinaryCondition`
- Category: `math/int`
- Output node: `False`

This node performs a binary condition operation on two integer inputs based on a specified operation (e.g., equality, greater than). It evaluates the condition and returns a boolean result indicating whether the condition holds.
## Input types
### Required
- **`op`**
    - Specifies the binary condition operation to perform on the inputs, such as equality or comparison. Its choice directly influences the result of the node, determining the nature of the condition being evaluated and thus affecting the boolean outcome.
    - Python dtype: `str`
    - Comfy dtype: `STRING`
- **`a`**
    - The first integer input for the binary condition operation. This value serves as one of the operands in the binary condition, playing a crucial role in the evaluation process and influencing the final boolean result.
    - Python dtype: `int`
    - Comfy dtype: `INT`
- **`b`**
    - The second integer input for the binary condition operation. Similar to the first operand, this value is essential in the condition evaluation, contributing to the determination of the boolean outcome.
    - Python dtype: `int`
    - Comfy dtype: `INT`
## Output types
- **`bool`**
    - The boolean result of the binary condition operation, indicating whether the specified condition between the two integer inputs holds.
    - Python dtype: `bool`
    - Comfy dtype: `BOOL`
## Usage tips
- Infra type: `CPU`
- Common nodes: unknown

Often used in decision-making processes within the pipeline to compare two integer values based on a specified operation (e.g., equality, greater than), and outputs a boolean result indicating the outcome of the comparison.
## Source code
```python
class IntBinaryCondition:
    @classmethod
    def INPUT_TYPES(cls) -> Mapping[str, Any]:
        return {
            "required": {
                "op": (list(INT_BINARY_CONDITIONS.keys()),),
                "a": DEFAULT_INT,
                "b": DEFAULT_INT,
            }
        }

    RETURN_TYPES = ("BOOL",)
    FUNCTION = "op"
    CATEGORY = "math/int"

    def op(self, op: str, a: int, b: int) -> tuple[bool]:
        return (INT_BINARY_CONDITIONS[op](a, b),)

```
