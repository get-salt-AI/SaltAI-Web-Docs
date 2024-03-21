# ImpactControlNetClearSEGS
## Documentation
- Class name: `ImpactControlNetClearSEGS`
- Category: `ImpactPack/Util`
- Output node: `False`

The `ImpactControlNetClearSEGS` node is designed to clear control networks from a given SEGS data structure. It iterates through each segment in the SEGS, maintaining all segment properties but removing any associated control network information.
## Input types
### Required
- **`segs`**
    - The SEGS data structure containing segments with associated control network information. This input is essential for the operation as it provides the segments to be processed.
    - Python dtype: `Tuple[Tuple[Any, List[SEG]]]`
    - Comfy dtype: `SEGS`
## Output types
- **`segs`**
    - Returns a modified SEGS data structure with the control network information removed from each segment.
    - Python dtype: `Tuple[Tuple[Any, List[SEG]]]`
    - Comfy dtype: `SEGS`
## Usage tips
- Infra type: `CPU`
- Common nodes: unknown


## Source code
```python
class ControlNetClearSEGS:
    @classmethod
    def INPUT_TYPES(s):
        return {"required": {"segs": ("SEGS",), }, }

    RETURN_TYPES = ("SEGS",)
    FUNCTION = "doit"

    CATEGORY = "ImpactPack/Util"

    def doit(self, segs):
        new_segs = []

        for seg in segs[1]:
            new_seg = SEG(seg.cropped_image, seg.cropped_mask, seg.confidence, seg.crop_region, seg.bbox, seg.label, None)
            new_segs.append(new_seg)

        return ((segs[0], new_segs), )

```
