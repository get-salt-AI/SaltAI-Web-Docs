# SEGS Concat
## Documentation
- Class name: `ImpactSEGSConcat`
- Category: `ImpactPack/Util`
- Output node: `False`

The `ImpactSEGSConcat` node is designed to concatenate multiple segmentation data (`SEGS`) based on their dimensions. It ensures that only `SEGS` with matching dimensions are concatenated, and provides error messages for mismatched dimensions.
## Input types
### Required
- **`segs1`**
    - Represents the first set of segmentation data to be concatenated. It is crucial for the operation as it sets the initial dimension to which subsequent `SEGS` will be compared and concatenated if they match.
    - Python dtype: `Tuple[Tuple[int, int], List[Any]]`
    - Comfy dtype: `SEGS`
## Output types
- **`segs`**
    - Outputs the concatenated segmentation data with their dimensions. If no valid `SEGS` are provided, it returns an empty `SEGS` structure.
    - Python dtype: `Tuple[Tuple[int, int], List[Any]]`
    - Comfy dtype: `SEGS`
## Usage tips
- Infra type: `CPU`
- Common nodes: `DetailerForEachDebugPipe`


## Source code
```python
class SEGSConcat:
    @classmethod
    def INPUT_TYPES(s):
        return {"required": {
                     "segs1": ("SEGS", ),
                     },
                }

    RETURN_TYPES = ("SEGS",)
    FUNCTION = "doit"

    CATEGORY = "ImpactPack/Util"

    def doit(self, **kwargs):
        dim = None
        res = None

        for k, v in list(kwargs.items()):
            if v[0] == (0, 0) or len(v[1]) == 0:
                continue

            if dim is None:
                dim = v[0]
                res = v[1]
            else:
                if v[0] == dim:
                    res = res + v[1]
                else:
                    print(f"ERROR: source shape of 'segs1'{dim} and '{k}'{v[0]} are different. '{k}' will be ignored")

        if dim is None:
            empty_segs = ((0, 0), [])
            return (empty_segs, )
        else:
            return ((dim, res), )

```
