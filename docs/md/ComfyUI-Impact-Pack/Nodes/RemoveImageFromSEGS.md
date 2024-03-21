# Remove Image from SEGS
## Documentation
- Class name: `RemoveImageFromSEGS`
- Category: `ImpactPack/Util`
- Output node: `False`

The `RemoveImageFromSEGS` node processes a collection of segmentation results (SEGS) to remove the image data associated with each segmentation, effectively leaving the segmentation masks and metadata intact. This operation is useful for scenarios where only the segmentation information is needed, without the original image data.
## Input types
### Required
- **`segs`**
    - The input `segs` parameter represents a collection of segmentation results, including both the segmentation masks and the associated image data. This node operates by removing the image data from each segmentation result.
    - Python dtype: `Tuple[Tuple[int, int], List[SEG]]`
    - Comfy dtype: `SEGS`
## Output types
- **`segs`**
    - The output is a modified collection of segmentation results (SEGS) with the image data removed from each segmentation, leaving only the masks and metadata.
    - Python dtype: `Tuple[Tuple[int, int], List[SEG]]`
    - Comfy dtype: `SEGS`
## Usage tips
- Infra type: `CPU`
- Common nodes: unknown


## Source code
```python
class RemoveImageFromSEGS:
    @classmethod
    def INPUT_TYPES(s):
        return {"required": {"segs": ("SEGS", ), }}

    RETURN_TYPES = ("SEGS", )
    FUNCTION = "doit"

    CATEGORY = "ImpactPack/Util"

    def doit(self, segs):
        results = []

        if len(segs[1]) > 0:
            for seg in segs[1]:
                new_seg = SEG(None, seg.cropped_mask, seg.confidence, seg.crop_region, seg.bbox, seg.label, seg.control_net_wrapper)
                results.append(new_seg)

            return ((segs[0], results), )
        else:
            return (segs, )

```
