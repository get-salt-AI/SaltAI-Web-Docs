# ComposeVec2
## Documentation
- Class name: `CM_ComposeVec2`
- Category: `math/conversion`
- Output node: `False`

The `CM_ComposeVec2` node constructs a 2D vector (Vec2) from two provided float values, representing the x and y components of the vector. This operation is fundamental in vector mathematics and is used for converting scalar values into a vector format for further vector-based calculations.
## Input types
### Required
- **`x`**
    - The x component of the Vec2. It defines the horizontal position or value in the 2D vector space.
    - Python dtype: `float`
    - Comfy dtype: `FLOAT`
- **`y`**
    - The y component of the Vec2. It defines the vertical position or value in the 2D vector space.
    - Python dtype: `float`
    - Comfy dtype: `FLOAT`
## Output types
- **`vec2`**
    - The resulting 2D vector constructed from the input x and y components.
    - Python dtype: `tuple[float, float]`
    - Comfy dtype: `VEC2`
## Usage tips
- Infra type: `CPU`
- Common nodes: unknown

Often used in vector-based calculations and transformations, the `CM_ComposeVec2` node takes two float values as input to construct a 2D vector, representing the x and y components, which is essential for operations requiring vector inputs or for converting scalar values into a vector format.
## Source code
```python
class ComposeVec2:
    @classmethod
    def INPUT_TYPES(cls) -> Mapping[str, Any]:
        return {
            "required": {
                "x": ("FLOAT", {"default": 0.0}),
                "y": ("FLOAT", {"default": 0.0}),
            }
        }

    RETURN_TYPES = ("VEC2",)
    FUNCTION = "op"
    CATEGORY = "math/conversion"

    def op(self, x: float, y: float) -> tuple[Vec2]:
        return ((x, y),)

```
