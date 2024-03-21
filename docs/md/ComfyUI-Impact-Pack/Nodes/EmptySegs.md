# EmptySegs
## Documentation
- Class name: `EmptySegs`
- Category: `ImpactPack/Util`
- Output node: `False`

The `EmptySegs` node is designed to generate an empty segmentation structure. This can be useful as a placeholder or initial value in workflows that manipulate segmentation data.
## Input types
### Required
## Output types
- **`segs`**
    - Returns an empty segmentation structure, consisting of a shape tuple set to (0, 0) and an empty list, indicating no segments.
    - Python dtype: `Tuple[Tuple[int, int], List[Any]]`
    - Comfy dtype: `SEGS`
## Usage tips
- Infra type: `CPU`
- Common nodes: unknown


## Source code
```python
class EmptySEGS:
    @classmethod
    def INPUT_TYPES(s):
        return {"required": {}, }

    RETURN_TYPES = ("SEGS",)
    FUNCTION = "doit"

    CATEGORY = "ImpactPack/Util"

    def doit(self):
        shape = 0, 0
        return ((shape, []),)

```
