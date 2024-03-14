# FloatToInt
## Documentation
- Class name: `CM_FloatToInt`
- Category: `math/conversion`
- Output node: `False`

Converts a floating-point number to an integer by truncating the decimal part. This operation is useful for scenarios where integer values are required from floating-point computations.
## Input types
### Required
- **`a`**
    - The floating-point number to be converted to an integer. This parameter is crucial as it directly determines the output integer value by truncating any decimal part.
    - Python dtype: `float`
    - Comfy dtype: `FLOAT`
## Output types
- **`int`**
    - The integer result obtained by truncating the decimal part of the input floating-point number.
    - Python dtype: `int`
    - Comfy dtype: `INT`
## Usage tips
- Infra type: `CPU`
- Common nodes: unknown

Often used in scenarios requiring integer values from floating-point computations, such as setting discrete parameters or indexing, by truncating the decimal part of the input floating-point number.
## Source code
```python
class FloatToInt:
    @classmethod
    def INPUT_TYPES(cls) -> Mapping[str, Any]:
        return {"required": {"a": ("FLOAT", {"default": 0.0})}}

    RETURN_TYPES = ("INT",)
    FUNCTION = "op"
    CATEGORY = "math/conversion"

    def op(self, a: float) -> tuple[int]:
        return (int(a),)

```
