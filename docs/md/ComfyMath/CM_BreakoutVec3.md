# BreakoutVec3
## Documentation
- Class name: `CM_BreakoutVec3`
- Category: `math/conversion`
- Output node: `False`

The `CM_BreakoutVec3` node is designed for decomposing a 3-dimensional vector into its individual components. This operation is useful in scenarios where the separate values of a vector are needed for further calculations or analysis.
## Input types
### Required
- **`a`**
    - The input vector to be decomposed. It is essential for operations that require access to the individual components of a vector for further processing.
    - Python dtype: `tuple[float, float, float]`
    - Comfy dtype: `VEC3`
## Output types
- **`float`**
    - The third component of the input vector.
    - Python dtype: `float`
    - Comfy dtype: `FLOAT`
## Usage tips
- Infra type: `CPU`
- Common nodes: unknown

Often used in scenarios requiring the manipulation or analysis of individual vector components, such as adjusting positions in 3D space or performing calculations on specific dimensions. The `CM_BreakoutVec3` node takes a 3-dimensional vector as input and outputs its three components separately for further processing or analysis.
## Source code
```python
class BreakoutVec3:
    @classmethod
    def INPUT_TYPES(cls) -> Mapping[str, Any]:
        return {"required": {"a": ("VEC3", {"default": VEC3_ZERO})}}

    RETURN_TYPES = ("FLOAT", "FLOAT", "FLOAT")
    FUNCTION = "op"
    CATEGORY = "math/conversion"

    def op(self, a: Vec3) -> tuple[float, float, float]:
        return (a[0], a[1], a[2])

```
