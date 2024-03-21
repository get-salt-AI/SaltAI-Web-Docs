# BoolBinaryOperation
## Documentation
- Class name: `CM_BoolBinaryOperation`
- Category: `math/bool`
- Output node: `False`

Performs a binary operation on two boolean inputs based on a specified operation. This node allows for the execution of various boolean logic operations such as AND, OR, XOR, etc., on the input booleans, producing a boolean result.
## Input types
### Required
- **`op`**
    - Specifies the boolean binary operation to be performed. The choice of operation affects the logic applied to the input booleans and consequently the result.
    - Python dtype: `str`
    - Comfy dtype: `STRING`
- **`a`**
    - The first boolean input for the binary operation. Acts as one of the operands in the boolean logic operation.
    - Python dtype: `bool`
    - Comfy dtype: `BOOL`
- **`b`**
    - The second boolean input for the binary operation. Acts as the other operand in the boolean logic operation.
    - Python dtype: `bool`
    - Comfy dtype: `BOOL`
## Output types
- **`bool`**
    - The result of applying the specified boolean binary operation on the input booleans.
    - Python dtype: `bool`
    - Comfy dtype: `BOOL`
## Usage tips
- Infra type: `CPU`
- Common nodes: unknown

Often used with conditional logic nodes to apply boolean operations like AND, OR, XOR on two input booleans, resulting in a single boolean output that can influence the flow or decisions within the AI pipeline.
## Source code
```python
class BoolBinaryOperation:
    @classmethod
    def INPUT_TYPES(cls) -> Mapping[str, Any]:
        return {
            "required": {
                "op": (list(BOOL_BINARY_OPERATIONS.keys()),),
                "a": DEFAULT_BOOL,
                "b": DEFAULT_BOOL,
            }
        }

    RETURN_TYPES = ("BOOL",)
    FUNCTION = "op"
    CATEGORY = "math/bool"

    def op(self, op: str, a: bool, b: bool) -> tuple[bool]:
        return (BOOL_BINARY_OPERATIONS[op](a, b),)

```
