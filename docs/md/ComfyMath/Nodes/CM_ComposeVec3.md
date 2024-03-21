# ComposeVec3
## Documentation
- Class name: `CM_ComposeVec3`
- Category: `math/conversion`
- Output node: `False`

The `ComposeVec3` node is designed for creating a 3-dimensional vector (Vec3) from three separate float values representing the x, y, and z coordinates. This operation is fundamental in vector mathematics and is often used in graphics programming, physics simulations, and other computational fields requiring spatial representation.
## Input types
### Required
- **`x`**
    - The 'x' input represents the x-coordinate of the Vec3 to be composed. It is a crucial component in defining the spatial position or direction of the vector in 3D space.
    - Python dtype: `float`
    - Comfy dtype: `FLOAT`
- **`y`**
    - The 'y' input signifies the y-coordinate of the Vec3. Along with 'x' and 'z', it helps in accurately defining the vector's position or orientation in a three-dimensional environment.
    - Python dtype: `float`
    - Comfy dtype: `FLOAT`
- **`z`**
    - The 'z' input denotes the z-coordinate of the Vec3. It completes the 3D spatial information required to fully specify the vector, working in conjunction with 'x' and 'y'.
    - Python dtype: `float`
    - Comfy dtype: `FLOAT`
## Output types
- **`vec3`**
    - The output is a Vec3, a 3-dimensional vector composed of the input x, y, and z coordinates. It's a fundamental data structure in 3D computations, useful for representing points, directions, or velocities in space.
    - Python dtype: `tuple[Vec3]`
    - Comfy dtype: `VEC3`
## Usage tips
- Infra type: `CPU`
- Common nodes: unknown

The `CM_ComposeVec3` node is essential for constructing a 3-dimensional vector from individual x, y, and z float values, crucial for applications in 3D graphics, physics simulations, and spatial analysis. It is often used with nodes that require precise spatial positioning or directionality in a three-dimensional environment.
## Source code
```python
class ComposeVec3:
    @classmethod
    def INPUT_TYPES(cls) -> Mapping[str, Any]:
        return {
            "required": {
                "x": ("FLOAT", {"default": 0.0}),
                "y": ("FLOAT", {"default": 0.0}),
                "z": ("FLOAT", {"default": 0.0}),
            }
        }

    RETURN_TYPES = ("VEC3",)
    FUNCTION = "op"
    CATEGORY = "math/conversion"

    def op(self, x: float, y: float, z: float) -> tuple[Vec3]:
        return ((x, y, z),)

```
