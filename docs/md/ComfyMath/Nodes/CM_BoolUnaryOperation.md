# BoolUnaryOperation
## Documentation
- Class name: `CM_BoolUnaryOperation`
- Category: `math/bool`
- Output node: `False`

Performs a unary boolean operation on a single boolean input. The operation is determined by the 'op' parameter, which selects from a predefined set of boolean operations such as logical NOT.
## Input types
### Required
- **`op`**
    - Specifies the unary boolean operation to be performed. The choice of operation affects the outcome of the node's execution.
    - Python dtype: `str`
    - Comfy dtype: `STRING`
- **`a`**
    - The boolean input on which the unary operation will be performed. The operation's effect is directly applied to this input.
    - Python dtype: `bool`
    - Comfy dtype: `BOOL`
## Output types
- **`bool`**
    - The result of applying the specified unary boolean operation on the input boolean value.
    - Python dtype: `bool`
    - Comfy dtype: `BOOL`
## Usage tips
- Infra type: `CPU`
- Common nodes: unknown

Often used in logical flow control and decision-making processes within the pipeline, the CM_BoolUnaryOperation node performs a specified unary boolean operation, such as logical NOT, on a single boolean input to produce a boolean output.
## Source code
```python
class BoolUnaryOperation:
    @classmethod
    def INPUT_TYPES(cls) -> Mapping[str, Any]:
        return {
            "required": {"op": (list(BOOL_UNARY_OPERATIONS.keys()),), "a": DEFAULT_BOOL}
        }

    RETURN_TYPES = ("BOOL",)
    FUNCTION = "op"
    CATEGORY = "math/bool"

    def op(self, op: str, a: bool) -> tuple[bool]:
        return (BOOL_UNARY_OPERATIONS[op](a),)

```
