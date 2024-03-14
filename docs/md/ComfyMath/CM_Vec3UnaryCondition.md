# Vec3UnaryCondition
## Documentation
- Class name: `CM_Vec3UnaryCondition`
- Category: `math/vec3`
- Output node: `False`

Performs a unary operation on a 3-dimensional vector based on a specified condition. This operation evaluates a condition on the vector and returns a boolean result.
## Input types
### Required
- **`op`**
    - Specifies the unary operation to be performed on the vector. The operation is selected from a predefined list of conditions. The choice of operation directly influences the evaluation process and the resulting boolean value, making it crucial for determining the condition's applicability to the vector.
    - Python dtype: `str`
    - Comfy dtype: `STRING`
- **`a`**
    - The 3-dimensional vector on which the unary operation is performed. It represents the input vector to be evaluated against the specified condition. The vector's dimensions and values play a significant role in the outcome of the condition check.
    - Python dtype: `Vec3`
    - Comfy dtype: `VEC3`
## Output types
- **`bool`**
    - The result of the unary operation as a boolean value, indicating whether the specified condition is met by the input vector.
    - Python dtype: `bool`
    - Comfy dtype: `BOOL`
## Usage tips
- Infra type: `CPU`
- Common nodes: unknown

Often used in scenarios requiring the evaluation of a 3-dimensional vector against a specific condition, such as determining if a vector falls within a certain range or meets a geometric constraint. The CM_Vec3UnaryCondition node takes a vector (`a`) and a condition operation (`op`) as input, and outputs a boolean value indicating whether the vector satisfies the specified condition.
## Source code
```python
class Vec3UnaryCondition:
    @classmethod
    def INPUT_TYPES(cls) -> Mapping[str, Any]:
        return {
            "required": {
                "op": (list(VEC_UNARY_CONDITIONS.keys()),),
                "a": DEFAULT_VEC3,
            }
        }

    RETURN_TYPES = ("BOOL",)
    FUNCTION = "op"
    CATEGORY = "math/vec3"

    def op(self, op: str, a: Vec3) -> tuple[bool]:
        return (VEC_UNARY_CONDITIONS[op](numpy.array(a)),)

```
