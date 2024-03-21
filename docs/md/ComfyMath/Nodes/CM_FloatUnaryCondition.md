# FloatUnaryCondition
## Documentation
- Class name: `CM_FloatUnaryCondition`
- Category: `math/float`
- Output node: `False`

This node performs unary operations on a single float value based on a specified condition. It evaluates the condition and returns a boolean indicating whether the condition holds for the given float value.
## Input types
### Required
- **`op`**
    - Specifies the unary operation to be performed. The operation is chosen from a predefined list of conditions such as 'IsZero', 'IsPositive', 'IsNegative', 'IsNonZero', 'IsPositiveInfinity', 'IsNegativeInfinity', 'IsNaN', 'IsFinite', 'IsInfinite', 'IsEven', and 'IsOdd', which evaluate to either true or false.
    - Python dtype: `str`
    - Comfy dtype: `STRING`
- **`a`**
    - The float value to be evaluated by the specified unary condition.
    - Python dtype: `float`
    - Comfy dtype: `FLOAT`
## Output types
- **`bool`**
    - The result of evaluating the specified unary condition on the input float value. It indicates whether the condition holds (true) or not (false).
    - Python dtype: `bool`
    - Comfy dtype: `BOOL`
## Usage tips
- Infra type: `CPU`
- Common nodes: unknown

Often used in decision-making processes within the pipeline to evaluate a single float value against a predefined condition, such as 'IsZero' or 'IsPositive', and returns a boolean indicating whether the condition is met. This node is crucial for filtering data or triggering specific pathways based on numerical conditions.
## Source code
```python
class FloatUnaryCondition:
    @classmethod
    def INPUT_TYPES(cls) -> Mapping[str, Any]:
        return {
            "required": {
                "op": (list(FLOAT_UNARY_CONDITIONS.keys()),),
                "a": DEFAULT_FLOAT,
            }
        }

    RETURN_TYPES = ("BOOL",)
    FUNCTION = "op"
    CATEGORY = "math/float"

    def op(self, op: str, a: float) -> tuple[bool]:
        return (FLOAT_UNARY_CONDITIONS[op](a),)

```
