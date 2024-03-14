# BreakoutVec2
## Documentation
- Class name: `CM_BreakoutVec2`
- Category: `math/conversion`
- Output node: `False`

This node breaks down a 2D vector into its individual components, providing a straightforward way to access and manipulate the x and y coordinates separately.
## Input types
### Required
- **`a`**
    - The 2D vector to be broken down. This allows for the individual manipulation of the x and y components of the vector.
    - Python dtype: `tuple[float, float]`
    - Comfy dtype: `VEC2`
## Output types
- **`float`**
    - The y component of the input 2D vector.
    - Python dtype: `float`
    - Comfy dtype: `FLOAT`
## Usage tips
- Infra type: `CPU`
- Common nodes: unknown

Often used in mathematical and conversion operations within the pipeline to separate a 2D vector into its x and y components, allowing for individual manipulation and analysis of these coordinates.
## Source code
```python
class BreakoutVec2:
    @classmethod
    def INPUT_TYPES(cls) -> Mapping[str, Any]:
        return {"required": {"a": ("VEC2", {"default": VEC2_ZERO})}}

    RETURN_TYPES = ("FLOAT", "FLOAT")
    FUNCTION = "op"
    CATEGORY = "math/conversion"

    def op(self, a: Vec2) -> tuple[float, float]:
        return (a[0], a[1])

```
