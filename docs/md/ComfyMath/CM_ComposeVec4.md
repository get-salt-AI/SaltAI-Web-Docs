# ComposeVec4
## Documentation
- Class name: `CM_ComposeVec4`
- Category: `math/conversion`
- Output node: `False`

The `CM_ComposeVec4` node is designed for creating a 4-dimensional vector (Vec4) from four individual floating-point values. This operation is fundamental in mathematical and graphical computations where a Vec4 is required.
## Input types
### Required
- **`x`**
    - Represents the first component of the Vec4. It's crucial for defining the vector's position or value along the x-axis.
    - Python dtype: `float`
    - Comfy dtype: `FLOAT`
- **`y`**
    - Defines the second component of the Vec4, essential for specifying the vector's position or value along the y-axis.
    - Python dtype: `float`
    - Comfy dtype: `FLOAT`
- **`z`**
    - Serves as the third component, vital for determining the vector's position or value along the z-axis.
    - Python dtype: `float`
    - Comfy dtype: `FLOAT`
- **`w`**
    - Represents the fourth component, often used for additional data like transparency in graphical applications.
    - Python dtype: `float`
    - Comfy dtype: `FLOAT`
## Output types
- **`vec4`**
    - The resulting 4-dimensional vector composed from the input values.
    - Python dtype: `Vec4`
    - Comfy dtype: `VEC4`
## Usage tips
- Infra type: `CPU`
- Common nodes: unknown

Often used in graphical computations and shader programming, the `CM_ComposeVec4` node allows for the creation of a 4-dimensional vector from individual x, y, z, and w floating-point values, essential for defining positions, colors, or other vector-based attributes in a scene.
## Source code
```python
class ComposeVec4:
    @classmethod
    def INPUT_TYPES(cls) -> Mapping[str, Any]:
        return {
            "required": {
                "x": ("FLOAT", {"default": 0.0}),
                "y": ("FLOAT", {"default": 0.0}),
                "z": ("FLOAT", {"default": 0.0}),
                "w": ("FLOAT", {"default": 0.0}),
            }
        }

    RETURN_TYPES = ("VEC4",)
    FUNCTION = "op"
    CATEGORY = "math/conversion"

    def op(self, x: float, y: float, z: float, w: float) -> tuple[Vec4]:
        return ((x, y, z, w),)

```
