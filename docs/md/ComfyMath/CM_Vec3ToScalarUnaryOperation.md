# Vec3ToScalarUnaryOperation
## Documentation
- Class name: `CM_Vec3ToScalarUnaryOperation`
- Category: `math/vec3`
- Output node: `False`

Performs a unary operation that transforms a 3-dimensional vector (Vec3) into a scalar value based on the specified operation. This operation is useful for extracting specific scalar properties or measurements from a Vec3.
## Input types
### Required
- **`op`**
    - Specifies the unary operation to be performed on the Vec3. The choice of operation determines how the Vec3 is transformed into a scalar value, affecting the outcome significantly.
    - Python dtype: `str`
    - Comfy dtype: `STRING`
- **`a`**
    - The Vec3 on which the unary operation is performed. This vector is the input from which the scalar value is derived.
    - Python dtype: `Vec3`
    - Comfy dtype: `VEC3`
## Output types
- **`float`**
    - The scalar result of the unary operation performed on the Vec3. This output provides a specific scalar measurement or property derived from the input Vec3.
    - Python dtype: `float`
    - Comfy dtype: `FLOAT`
## Usage tips
- Infra type: `GPU`
- Common nodes: unknown

Often used in mathematical and geometric computations to convert a 3-dimensional vector (Vec3) into a meaningful scalar value, such as length, by applying a specified unary operation. This node is crucial for extracting specific measurements or properties from a Vec3 for further analysis or use in the pipeline.
## Source code
```python
class Vec3ToScalarUnaryOperation:
    @classmethod
    def INPUT_TYPES(cls) -> Mapping[str, Any]:
        return {
            "required": {
                "op": (list(VEC_TO_SCALAR_UNARY_OPERATION.keys()),),
                "a": DEFAULT_VEC3,
            }
        }

    RETURN_TYPES = ("FLOAT",)
    FUNCTION = "op"
    CATEGORY = "math/vec3"

    def op(self, op: str, a: Vec3) -> tuple[float]:
        return (VEC_TO_SCALAR_UNARY_OPERATION[op](numpy.array(a)),)

```
