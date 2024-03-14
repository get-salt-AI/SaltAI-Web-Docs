# IntUnaryOperation
## Documentation
- Class name: `CM_IntUnaryOperation`
- Category: `math/int`
- Output node: `False`

Performs a unary operation on an integer. This operation is defined by a specific operation (op) applied to an integer (a), resulting in a modified integer value.
## Input types
### Required
- **`op`**
    - Specifies the unary operation to be performed on the integer. The choice of operation significantly affects the modification of the input integer, altering its value based on the operation selected.
    - Python dtype: `str`
    - Comfy dtype: `STRING`
- **`a`**
    - The integer on which the unary operation is to be performed. It serves as the input to the operation defined by 'op'.
    - Python dtype: `int`
    - Comfy dtype: `INT`
## Output types
- **`int`**
    - The result of applying the specified unary operation on the input integer. It is an integer reflecting the outcome of the operation.
    - Python dtype: `int`
    - Comfy dtype: `INT`
## Usage tips
- Infra type: `CPU`
- Common nodes: unknown

Often used in mathematical and data transformation pipelines to apply a specific unary operation, such as incrementing or negating, on an input integer `a`, resulting in a modified integer output.
## Source code
```python
class IntUnaryOperation:
    @classmethod
    def INPUT_TYPES(cls) -> Mapping[str, Any]:
        return {
            "required": {"op": (list(INT_UNARY_OPERATIONS.keys()),), "a": DEFAULT_INT}
        }

    RETURN_TYPES = ("INT",)
    FUNCTION = "op"
    CATEGORY = "math/int"

    def op(self, op: str, a: int) -> tuple[int]:
        return (INT_UNARY_OPERATIONS[op](a),)

```
