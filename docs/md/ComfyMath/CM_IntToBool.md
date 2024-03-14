# IntToBool
## Documentation
- Class name: `CM_IntToBool`
- Category: `math/conversion`
- Output node: `False`

The `CM_IntToBool` node converts an integer input to a boolean output. This operation is based on the principle that any non-zero integer will be considered `True`, while zero is considered `False`.
## Input types
### Required
- **`a`**
    - The integer input to be converted to a boolean. This parameter determines the boolean output based on its value being zero (False) or non-zero (True).
    - Python dtype: `int`
    - Comfy dtype: `INT`
## Output types
- **`bool`**
    - The boolean result of the conversion, indicating whether the input integer was non-zero (True) or zero (False).
    - Python dtype: `bool`
    - Comfy dtype: `BOOL`
## Usage tips
- Infra type: `CPU`
- Common nodes: unknown

Often used in decision-making processes within the pipeline, where the need to convert numerical thresholds into boolean flags is essential, such as enabling or disabling subsequent AI model nodes based on integer inputs.
## Source code
```python
class IntToBool:
    @classmethod
    def INPUT_TYPES(cls) -> Mapping[str, Any]:
        return {"required": {"a": ("INT", {"default": 0})}}

    RETURN_TYPES = ("BOOL",)
    FUNCTION = "op"
    CATEGORY = "math/conversion"

    def op(self, a: int) -> tuple[bool]:
        return (a != 0,)

```
