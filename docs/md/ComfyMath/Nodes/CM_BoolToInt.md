# BoolToInt
## Documentation
- Class name: `CM_BoolToInt`
- Category: `math/conversion`
- Output node: `False`

Converts a boolean value to an integer. This operation is a basic type conversion in mathematical computations, allowing for the transformation of true/false values into 0/1 integer representations.
## Input types
### Required
- **`a`**
    - The boolean input to be converted to an integer. This parameter is fundamental for the conversion process, determining the output based on its truth value.
    - Python dtype: `bool`
    - Comfy dtype: `BOOL`
## Output types
- **`int`**
    - The integer representation of the input boolean value. This output is crucial for further mathematical operations that require integer inputs.
    - Python dtype: `int`
    - Comfy dtype: `INT`
## Usage tips
- Infra type: `CPU`
- Common nodes: unknown

Often used in mathematical and logical operations where a boolean input needs to be converted to an integer for further processing, enabling seamless integration with nodes that require integer inputs for computations.
## Source code
```python
class BoolToInt:
    @classmethod
    def INPUT_TYPES(cls) -> Mapping[str, Any]:
        return {"required": {"a": ("BOOL", {"default": False})}}

    RETURN_TYPES = ("INT",)
    FUNCTION = "op"
    CATEGORY = "math/conversion"

    def op(self, a: bool) -> tuple[int]:
        return (int(a),)

```
