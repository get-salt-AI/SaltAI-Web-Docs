# ImpactDummyInput
## Documentation
- Class name: `ImpactDummyInput`
- Category: `ImpactPack/Debug`
- Output node: `False`

The `ImpactDummyInput` node is designed to generate a dummy input value. It serves as a placeholder or a default value generator within the ImpactPack/Debug category, primarily used for testing or debugging purposes.
## Input types
### Required
## Output types
- **`*`**
    - unknown
    - Python dtype: `unknown`
    - Comfy dtype: `*`
## Usage tips
- Infra type: `CPU`
- Common nodes: unknown


## Source code
```python
class ImpactDummyInput:
    @classmethod
    def INPUT_TYPES(s):
        return {"required": {}}

    CATEGORY = "ImpactPack/Debug"

    RETURN_TYPES = (any_typ,)
    FUNCTION = "doit"

    def doit(self):
        return ("DUMMY",)

```
