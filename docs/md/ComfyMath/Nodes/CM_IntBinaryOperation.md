# IntBinaryOperation
## Documentation
- Class name: `CM_IntBinaryOperation`
- Category: `math/int`
- Output node: `False`

Performs a binary operation on two integer inputs based on a specified operation. This node allows for basic arithmetic or comparison operations between integers, producing an integer result.
## Input types
### Required
- **`op`**
    - Determines the specific binary operation (e.g., addition, subtraction) to apply to the integer inputs. The choice of operation directly influences the nature of the computation and the final outcome, making it a crucial aspect of the node's functionality.
    - Python dtype: `str`
    - Comfy dtype: `STRING`
- **`a`**
    - Represents the first operand in the binary operation. Its value is essential as it serves as one of the two primary inputs upon which the specified operation is executed, directly impacting the result.
    - Python dtype: `int`
    - Comfy dtype: `INT`
- **`b`**
    - Serves as the second operand in the binary operation. Similar to the first operand, its value is critical as it contributes to the computation process, affecting the overall result of the operation.
    - Python dtype: `int`
    - Comfy dtype: `INT`
## Output types
- **`int`**
    - The integer result of the specified binary operation on the inputs.
    - Python dtype: `int`
    - Comfy dtype: `INT`
## Usage tips
- Infra type: `CPU`
- Common nodes: unknown

Often used in mathematical and logical operations within the pipeline, the CM_IntBinaryOperation node performs specified arithmetic or comparison operations on two integer inputs, producing an integer result.
## Source code
```python
class IntBinaryOperation:
    @classmethod
    def INPUT_TYPES(cls) -> Mapping[str, Any]:
        return {
            "required": {
                "op": (list(INT_BINARY_OPERATIONS.keys()),),
                "a": DEFAULT_INT,
                "b": DEFAULT_INT,
            }
        }

    RETURN_TYPES = ("INT",)
    FUNCTION = "op"
    CATEGORY = "math/int"

    def op(self, op: str, a: int, b: int) -> tuple[int]:
        return (INT_BINARY_OPERATIONS[op](a, b),)

```
