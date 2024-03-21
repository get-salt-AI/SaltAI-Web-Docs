# FloatUnaryOperation
## Documentation
- Class name: `CM_FloatUnaryOperation`
- Category: `math/float`
- Output node: `False`

Performs a unary operation on a single float value. The operation is selected from a predefined list of float unary operations, and applied to the input float, producing a single float result.
## Input types
### Required
- **`op`**
    - Specifies the unary operation to be performed on the input float. The operation is chosen from a predefined list of available float unary operations, which determines how the input float is processed. The choice of operation directly influences the computation performed on the input, affecting the node's output in a specific manner. Understanding the specific operation selected is crucial as it dictates the mathematical transformation applied to the input, thereby shaping the final output in a distinct way.
    - Python dtype: `str`
    - Comfy dtype: `STRING`
- **`a`**
    - The input float value to which the specified unary operation is applied. This value is the subject of the unary operation, determining the output of the node. The nature of this input is crucial as it directly impacts the result of the operation, influencing the computation's outcome. The magnitude and sign of this input float significantly affect the operation's effect, thereby altering the computation's final result.
    - Python dtype: `float`
    - Comfy dtype: `FLOAT`
## Output types
- **`float`**
    - The result of applying the specified unary operation to the input float value. This output is a single float, representing the outcome of the operation.
    - Python dtype: `float`
    - Comfy dtype: `FLOAT`
## Usage tips
- Infra type: `CPU`
- Common nodes: unknown

The CM_FloatUnaryOperation node is utilized for applying a specific unary operation, chosen from a predefined list, to a single float input value, resulting in a single float output. This node is often used in mathematical and data transformation pipelines to modify the magnitude or sign of an input float, thereby altering its value according to the selected operation.
## Source code
```python
class FloatUnaryOperation:
    @classmethod
    def INPUT_TYPES(cls) -> Mapping[str, Any]:
        return {
            "required": {
                "op": (list(FLOAT_UNARY_OPERATIONS.keys()),),
                "a": DEFAULT_FLOAT,
            }
        }

    RETURN_TYPES = ("FLOAT",)
    FUNCTION = "op"
    CATEGORY = "math/float"

    def op(self, op: str, a: float) -> tuple[float]:
        return (FLOAT_UNARY_OPERATIONS[op](a),)

```
