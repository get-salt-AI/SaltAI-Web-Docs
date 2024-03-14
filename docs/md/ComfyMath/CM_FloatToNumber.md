# FloatToNumber
## Documentation
- Class name: `CM_FloatToNumber`
- Category: `math/conversion`
- Output node: `False`

The `CM_FloatToNumber` node converts a floating-point value to a generic number type. This operation is fundamental in scenarios where a uniform number type is required across different mathematical operations.
## Input types
### Required
- **`a`**
    - The floating-point value to be converted into a generic number type. This parameter is essential for the conversion process.
    - Python dtype: `float`
    - Comfy dtype: `FLOAT`
## Output types
- **`number`**
    - The result of the conversion, represented as a generic number type.
    - Python dtype: `float`
    - Comfy dtype: `NUMBER`
## Usage tips
- Infra type: `CPU`
- Common nodes: unknown

Often used in mathematical operations and data normalization processes where a uniform number type is essential, the `CM_FloatToNumber` node converts a floating-point value into a generic number type, ensuring compatibility across various computational tasks.
## Source code
```python
class FloatToNumber:
    @classmethod
    def INPUT_TYPES(cls) -> Mapping[str, Any]:
        return {"required": {"a": ("FLOAT", {"default": 0.0})}}

    RETURN_TYPES = ("NUMBER",)
    FUNCTION = "op"
    CATEGORY = "math/conversion"

    def op(self, a: float) -> tuple[number]:
        return (a,)

```
