# Vec2BinaryCondition
## Documentation
- Class name: `CM_Vec2BinaryCondition`
- Category: `math/vec2`
- Output node: `False`

Performs a binary condition operation between two 2-dimensional vectors based on a specified operation. This operation evaluates to a boolean value indicating the result of the condition.
## Input types
### Required
- **`op`**
    - Specifies the binary condition operation to be performed between the two vectors. The choice of operation determines how the vectors are compared.
    - Python dtype: `List[str]`
    - Comfy dtype: `STRING`
- **`a`**
    - The first 2-dimensional vector involved in the binary condition operation.
    - Python dtype: `Vec2`
    - Comfy dtype: `VEC2`
- **`b`**
    - The second 2-dimensional vector involved in the binary condition operation.
    - Python dtype: `Vec2`
    - Comfy dtype: `VEC2`
## Output types
- **`bool`**
    - The result of the binary condition operation as a boolean value, indicating whether the specified condition holds true between the two vectors.
    - Python dtype: `bool`
    - Comfy dtype: `BOOL`
## Usage tips
- Infra type: `CPU`
- Common nodes: unknown

Often used in decision-making processes within the pipeline, where comparing two 2-dimensional vectors is necessary to determine the flow based on a specified condition, such as equality or magnitude comparison. The CM_Vec2BinaryCondition node takes two vectors as input and outputs a boolean value indicating whether the specified condition holds true between them.
## Source code
```python
class Vec2BinaryCondition:
    @classmethod
    def INPUT_TYPES(cls) -> Mapping[str, Any]:
        return {
            "required": {
                "op": (list(VEC_BINARY_CONDITIONS.keys()),),
                "a": DEFAULT_VEC2,
                "b": DEFAULT_VEC2,
            }
        }

    RETURN_TYPES = ("BOOL",)
    FUNCTION = "op"
    CATEGORY = "math/vec2"

    def op(self, op: str, a: Vec2, b: Vec2) -> tuple[bool]:
        return (VEC_BINARY_CONDITIONS[op](numpy.array(a), numpy.array(b)),)

```
