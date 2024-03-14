# IntToFloat
## Documentation
- Class name: `CM_IntToFloat`
- Category: `math/conversion`
- Output node: `False`

Converts an integer value to a floating-point number. This operation is a basic type conversion in mathematical computations, allowing for more precise calculations that require decimal points.
## Input types
### Required
- **`a`**
    - The integer value to be converted into a floating-point number. This conversion is essential for operations that require decimal precision.
    - Python dtype: `int`
    - Comfy dtype: `INT`
## Output types
- **`float`**
    - The resulting floating-point number after conversion. This allows for more precise mathematical operations that involve decimals.
    - Python dtype: `float`
    - Comfy dtype: `FLOAT`
## Usage tips
- Infra type: `CPU`
- Common nodes: unknown

Often used in mathematical and computational pipelines requiring decimal precision, the CM_IntToFloat node converts an integer input ('a') into a floating-point number, facilitating operations that involve decimals.
## Source code
```python
class IntToFloat:
    @classmethod
    def INPUT_TYPES(cls) -> Mapping[str, Any]:
        return {"required": {"a": ("INT", {"default": 0})}}

    RETURN_TYPES = ("FLOAT",)
    FUNCTION = "op"
    CATEGORY = "math/conversion"

    def op(self, a: int) -> tuple[float]:
        return (float(a),)

```
