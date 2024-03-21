# BreakoutVec4
## Documentation
- Class name: `CM_BreakoutVec4`
- Category: `math/conversion`
- Output node: `False`

The `CM_BreakoutVec4` node is designed for decomposing a 4-dimensional vector into its individual components. This operation is useful in scenarios where the separate values of a vector are needed for further individual processing or analysis.
## Input types
### Required
- **`a`**
    - This parameter represents the 4-dimensional vector to be decomposed. It is essential for the operation as it provides the data that will be broken down into individual float values.
    - Python dtype: `tuple[float, float, float, float]`
    - Comfy dtype: `VEC4`
## Output types
- **`float`**
    - The fourth component of the input vector.
    - Python dtype: `float`
    - Comfy dtype: `FLOAT`
## Usage tips
- Infra type: `CPU`
- Common nodes: unknown

Often used in scenarios requiring individual manipulation or analysis of the components of a 4-dimensional vector, such as adjusting specific properties in graphics or data visualization tasks.
## Source code
```python
class BreakoutVec4:
    @classmethod
    def INPUT_TYPES(cls) -> Mapping[str, Any]:
        return {"required": {"a": ("VEC4", {"default": VEC4_ZERO})}}

    RETURN_TYPES = ("FLOAT", "FLOAT", "FLOAT", "FLOAT")
    FUNCTION = "op"
    CATEGORY = "math/conversion"

    def op(self, a: Vec4) -> tuple[float, float, float, float]:
        return (a[0], a[1], a[2], a[3])

```
