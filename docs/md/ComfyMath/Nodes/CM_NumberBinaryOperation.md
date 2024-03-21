# NumberBinaryOperation
## Documentation
- Class name: `CM_NumberBinaryOperation`
- Category: `math/number`
- Output node: `False`

Performs a binary operation on two numbers based on a specified operation. This operation can be any function defined in FLOAT_BINARY_OPERATIONS, allowing for a wide range of mathematical computations between two numbers.
## Input types
### Required
- **`op`**
    - Specifies the binary operation to perform. The choice of operation significantly influences the result, as it determines how the two input numbers are mathematically combined.
    - Python dtype: `str`
    - Comfy dtype: `STRING`
- **`a`**
    - The first input number for the binary operation. Acts as one of the operands in the mathematical computation.
    - Python dtype: `float or int`
    - Comfy dtype: `NUMBER`
- **`b`**
    - The second input number for the binary operation. Serves as the other operand, together with 'a', in the computation.
    - Python dtype: `float or int`
    - Comfy dtype: `NUMBER`
## Output types
- **`number`**
    - The result of the binary operation performed on the two input numbers.
    - Python dtype: `float`
    - Comfy dtype: `NUMBER`
## Usage tips
- Infra type: `CPU`
- Common nodes: `CM_NumberToInt`

The CM_NumberBinaryOperation node is crucial for performing mathematical adjustments or calculations between numerical values within the pipeline, such as adjusting image dimensions or calculating aspect ratios. It accepts two numerical inputs, applies a specified binary operation (addition, subtraction, multiplication, or division), and outputs the resulting number, facilitating precise control over numerical parameters in AI-driven video content creation.
## Source code
```python
class NumberBinaryOperation:
    @classmethod
    def INPUT_TYPES(cls) -> Mapping[str, Any]:
        return {
            "required": {
                "op": (list(FLOAT_BINARY_OPERATIONS.keys()),),
                "a": DEFAULT_NUMBER,
                "b": DEFAULT_NUMBER,
            }
        }

    RETURN_TYPES = ("NUMBER",)
    FUNCTION = "op"
    CATEGORY = "math/number"

    def op(self, op: str, a: number, b: number) -> tuple[float]:
        return (FLOAT_BINARY_OPERATIONS[op](float(a), float(b)),)

```
