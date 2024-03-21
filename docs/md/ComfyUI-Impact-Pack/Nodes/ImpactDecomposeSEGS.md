# Decompose (SEGS)
## Documentation
- Class name: `ImpactDecomposeSEGS`
- Category: `ImpactPack/Util`
- Output node: `False`

The `ImpactDecomposeSEGS` node is designed to decompose a given SEGS (segmentation elements) into its constituent parts without altering them. This operation is useful for further processing or analysis of the individual segments.
## Input types
### Required
- **`segs`**
    - The 'segs' parameter represents the segmentation elements to be decomposed. It is crucial for specifying the input segmentation that needs to be broken down into its constituent parts.
    - Python dtype: `Tuple[str, List[Any]]`
    - Comfy dtype: `SEGS`
## Output types
- **`segs_header`**
    - Returns the header information of the input SEGS, which typically includes dimensions or other metadata.
    - Python dtype: `str`
    - Comfy dtype: `SEGS_HEADER`
- **`seg_elt`**
    - Returns a list of individual segment elements extracted from the input SEGS.
    - Python dtype: `List[Any]`
    - Comfy dtype: `SEG_ELT`
## Usage tips
- Infra type: `CPU`
- Common nodes: unknown


## Source code
```python
class DecomposeSEGS:
    @classmethod
    def INPUT_TYPES(s):
        return {"required": {
                     "segs": ("SEGS", ),
                     },
                }

    RETURN_TYPES = ("SEGS_HEADER", "SEG_ELT",)
    OUTPUT_IS_LIST = (False, True, )

    FUNCTION = "doit"

    CATEGORY = "ImpactPack/Util"

    def doit(self, segs):
        return segs

```
