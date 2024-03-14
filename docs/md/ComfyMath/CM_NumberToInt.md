# NumberToInt
## Documentation
- Class name: `CM_NumberToInt`
- Category: `math/conversion`
- Output node: `False`

Converts a number (either integer or float) to an integer. This operation is useful for scenarios where integer values are required, such as indexing or discrete operations.
## Input types
### Required
- **`a`**
    - The number to be converted to an integer. This can be either an integer or a float, making the node versatile for different numeric types.
    - Python dtype: `Union[int, float]`
    - Comfy dtype: `NUMBER`
## Output types
- **`int`**
    - The integer representation of the input number.
    - Python dtype: `int`
    - Comfy dtype: `INT`
## Usage tips
- Infra type: `CPU`
- Common nodes: `SVD_img2vid_Conditioning`

The CM_NumberToInt node is crucial for converting numerical values from floating-point to integer format, often used in scenarios requiring integer inputs, such as indexing or discrete operations within the pipeline.
## Source code
```python
class NumberToInt:
    @classmethod
    def INPUT_TYPES(cls) -> Mapping[str, Any]:
        return {"required": {"a": ("NUMBER", {"default": 0.0})}}

    RETURN_TYPES = ("INT",)
    FUNCTION = "op"
    CATEGORY = "math/conversion"

    def op(self, a: number) -> tuple[int]:
        return (int(a),)

```
