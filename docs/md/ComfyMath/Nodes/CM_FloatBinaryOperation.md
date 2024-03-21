# FloatBinaryOperation
## Documentation
- Class name: `CM_FloatBinaryOperation`
- Category: `math/float`
- Output node: `False`

Executes a binary operation on two float inputs based on a specified operation. It supports a variety of operations such as addition, subtraction, multiplication, and division, allowing for flexible mathematical manipulations of float values.
## Input types
### Required
- **`op`**
    - Specifies the binary operation to be performed on the inputs. The choice of operation directly influences the result, enabling diverse mathematical computations.
    - Python dtype: `str`
    - Comfy dtype: `STRING`
- **`a`**
    - The first float input for the binary operation. Acts as one of the operands in the mathematical computation.
    - Python dtype: `float`
    - Comfy dtype: `FLOAT`
- **`b`**
    - The second float input for the binary operation. Together with the first input, it completes the operands needed for the computation.
    - Python dtype: `float`
    - Comfy dtype: `FLOAT`
## Output types
- **`float`**
    - The result of the binary operation performed on the two float inputs, returned as a float value.
    - Python dtype: `float`
    - Comfy dtype: `FLOAT`
## Usage tips
- Infra type: `CPU`
- Common nodes: unknown

Often used in mathematical computations and data transformations, the CM_FloatBinaryOperation node performs a specified binary operation (addition, subtraction, multiplication, or division) on two float inputs, producing a single float output.
## Source code
```python
class FloatBinaryOperation:
    @classmethod
    def INPUT_TYPES(cls) -> Mapping[str, Any]:
        return {
            "required": {
                "op": (list(FLOAT_BINARY_OPERATIONS.keys()),),
                "a": DEFAULT_FLOAT,
                "b": DEFAULT_FLOAT,
            }
        }

    RETURN_TYPES = ("FLOAT",)
    FUNCTION = "op"
    CATEGORY = "math/float"

    def op(self, op: str, a: float, b: float) -> tuple[float]:
        return (FLOAT_BINARY_OPERATIONS[op](a, b),)

```
