# SEGS isn't Empty
## Documentation
- Class name: `ImpactIsNotEmptySEGS`
- Category: `ImpactPack/Logic`
- Output node: `False`

This node checks if the input SEGS (segmentation data) is not empty. It returns a boolean value indicating whether there are any segments present in the input SEGS.
## Input types
### Required
- **`segs`**
    - The SEGS input represents segmentation data. This node checks if there are any segments within this data, which affects whether the output is true (not empty) or false (empty).
    - Python dtype: `Tuple[Tuple[int, int], List[Any]]`
    - Comfy dtype: `SEGS`
## Output types
- **`boolean`**
    - A boolean value indicating whether the input SEGS contains any segments. True if not empty, false otherwise.
    - Python dtype: `bool`
    - Comfy dtype: `BOOLEAN`
## Usage tips
- Infra type: `CPU`
- Common nodes: unknown


## Source code
```python
class ImpactNotEmptySEGS:
    @classmethod
    def INPUT_TYPES(cls):
        return {"required": {"segs": ("SEGS",)}}

    FUNCTION = "doit"
    CATEGORY = "ImpactPack/Logic"

    RETURN_TYPES = ("BOOLEAN", )

    def doit(self, segs):
        return (segs[1] != [], )

```
