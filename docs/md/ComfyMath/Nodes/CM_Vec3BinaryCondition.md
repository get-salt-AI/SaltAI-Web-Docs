# Vec3BinaryCondition
## Documentation
- Class name: `CM_Vec3BinaryCondition`
- Category: `math/vec3`
- Output node: `False`

This node performs a binary condition operation between two 3-dimensional vectors (Vec3) based on a specified operation. It evaluates the condition and returns a boolean result indicating whether the condition holds.
## Input types
### Required
- **`op`**
    - Specifies the binary condition operation to be performed between the two vectors. The choice of operation affects how the condition is evaluated and ultimately determines the boolean result.
    - Python dtype: `str`
    - Comfy dtype: `STRING`
- **`a`**
    - The first 3-dimensional vector involved in the binary condition operation.
    - Python dtype: `Vec3`
    - Comfy dtype: `VEC3`
- **`b`**
    - The second 3-dimensional vector involved in the binary condition operation.
    - Python dtype: `Vec3`
    - Comfy dtype: `VEC3`
## Output types
- **`bool`**
    - The boolean outcome of the binary condition operation, indicating whether the specified condition holds between the two vectors.
    - Python dtype: `bool`
    - Comfy dtype: `BOOL`
## Usage tips
- Infra type: `CPU`
- Common nodes: unknown

Often used in scenarios requiring comparison between two 3-dimensional vectors, such as determining spatial relationships or conditions in simulations and visualizations, by evaluating a specified binary condition and outputting a boolean result indicating whether the condition holds.
## Source code
```python
class Vec3BinaryCondition:
    @classmethod
    def INPUT_TYPES(cls) -> Mapping[str, Any]:
        return {
            "required": {
                "op": (list(VEC_BINARY_CONDITIONS.keys()),),
                "a": DEFAULT_VEC3,
                "b": DEFAULT_VEC3,
            }
        }

    RETURN_TYPES = ("BOOL",)
    FUNCTION = "op"
    CATEGORY = "math/vec3"

    def op(self, op: str, a: Vec3, b: Vec3) -> tuple[bool]:
        return (VEC_BINARY_CONDITIONS[op](numpy.array(a), numpy.array(b)),)

```
