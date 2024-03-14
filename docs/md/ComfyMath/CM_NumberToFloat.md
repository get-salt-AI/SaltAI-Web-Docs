# NumberToFloat
## Documentation
- Class name: `CM_NumberToFloat`
- Category: `math/conversion`
- Output node: `False`

Converts a number (either integer or float) to a floating-point representation. This operation is useful for ensuring that numerical inputs are in a consistent format for further mathematical operations.
## Input types
### Required
- **`a`**
    - The number to be converted to a floating-point representation. This conversion is essential for maintaining numerical consistency across operations that require floating-point inputs.
    - Python dtype: `Union[int, float]`
    - Comfy dtype: `NUMBER`
## Output types
- **`float`**
    - The floating-point representation of the input number. This output is crucial for operations that specifically require floating-point numbers.
    - Python dtype: `float`
    - Comfy dtype: `FLOAT`
## Usage tips
- Infra type: `CPU`
- Common nodes: `FaceDetailer,CR Integer Multiple`

The CM_NumberToFloat node is essential for converting numerical input data, whether integer or another number format, to a floating-point format. This conversion is crucial for ensuring numerical consistency and precision in operations that require floating-point inputs, such as scaling factors or detailed adjustments in advanced facial enhancement and transformation pipelines.
## Source code
```python
class NumberToFloat:
    @classmethod
    def INPUT_TYPES(cls) -> Mapping[str, Any]:
        return {"required": {"a": ("NUMBER", {"default": 0.0})}}

    RETURN_TYPES = ("FLOAT",)
    FUNCTION = "op"
    CATEGORY = "math/conversion"

    def op(self, a: number) -> tuple[float]:
        return (float(a),)

```
