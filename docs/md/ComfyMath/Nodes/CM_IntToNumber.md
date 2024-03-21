# IntToNumber
## Documentation
- Class name: `CM_IntToNumber`
- Category: `math/conversion`
- Output node: `False`

Converts an integer to a generic number format. This operation is a basic type conversion that allows integers to be used in contexts where a more general number type is expected.
## Input types
### Required
- **`a`**
    - The integer value to be converted into a generic number format. This conversion is straightforward and preserves the value.
    - Python dtype: `int`
    - Comfy dtype: `INT`
## Output types
- **`number`**
    - The result of converting the input integer to a generic number format. The output is directly equivalent to the input value, but in a more flexible number format.
    - Python dtype: `int | float`
    - Comfy dtype: `NUMBER`
## Usage tips
- Infra type: `CPU`
- Common nodes: `CM_NumberBinaryOperation`

Often used with nodes requiring numerical inputs in non-integer formats, such as the Eff. Loader SDXL for combining model outputs and configurations, ensuring seamless data type compatibility and processing efficiency within the pipeline.
## Source code
```python
class IntToNumber:
    @classmethod
    def INPUT_TYPES(cls) -> Mapping[str, Any]:
        return {"required": {"a": ("INT", {"default": 0})}}

    RETURN_TYPES = ("NUMBER",)
    FUNCTION = "op"
    CATEGORY = "math/conversion"

    def op(self, a: int) -> tuple[number]:
        return (a,)

```
