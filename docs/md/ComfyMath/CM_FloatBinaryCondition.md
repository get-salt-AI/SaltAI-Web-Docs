# FloatBinaryCondition
## Documentation
- Class name: `CM_FloatBinaryCondition`
- Category: `math/float`
- Output node: `False`

This node evaluates a binary condition between two float values based on a specified operation. It supports a variety of conditions such as equality, inequality, greater than, less than, etc., and returns a boolean result indicating whether the condition holds.
## Input types
### Required
- **`op`**
    - Specifies the binary condition operation to be applied between the two float values. The choice of operation determines how the values are compared and affects the boolean result of the node.
    - Python dtype: `str`
    - Comfy dtype: `STRING`
- **`a`**
    - The first float value to be compared. It plays a crucial role in the binary condition evaluation.
    - Python dtype: `float`
    - Comfy dtype: `FLOAT`
- **`b`**
    - The second float value to be compared. Together with the first value, it determines the outcome of the binary condition.
    - Python dtype: `float`
    - Comfy dtype: `FLOAT`
## Output types
- **`bool`**
    - The boolean result of the binary condition evaluation, indicating whether the specified condition holds between the two float values.
    - Python dtype: `bool`
    - Comfy dtype: `BOOL`
## Usage tips
- Infra type: `CPU`
- Common nodes: unknown

The CM_FloatBinaryCondition node is utilized for evaluating a binary condition (such as equality, inequality, greater than, less than, etc.) between two float values, returning a boolean result that indicates whether the specified condition is met. It is often used in decision-making processes within the pipeline, where the outcome of the condition can influence subsequent actions or the flow of data.
## Source code
```python
class FloatBinaryCondition:
    @classmethod
    def INPUT_TYPES(cls) -> Mapping[str, Any]:
        return {
            "required": {
                "op": (list(FLOAT_BINARY_CONDITIONS.keys()),),
                "a": DEFAULT_FLOAT,
                "b": DEFAULT_FLOAT,
            }
        }

    RETURN_TYPES = ("BOOL",)
    FUNCTION = "op"
    CATEGORY = "math/float"

    def op(self, op: str, a: float, b: float) -> tuple[bool]:
        return (FLOAT_BINARY_CONDITIONS[op](a, b),)

```
