# Bitwise(SEGS & MASKS ForEach)
## Documentation
- Class name: `ImpactSegsAndMaskForEach`
- Category: `ImpactPack/Operation`
- Output node: `False`

The `ImpactSegsAndMaskForEach` node applies a mask to each segmentation (seg) in a collection of segmentations. It operates by performing a bitwise AND operation between the mask and each segmentation's cropped mask, effectively filtering the segmentations based on the mask.
## Input types
### Required
- **`segs`**
    - The collection of segmentations to which the masks will be applied. Each segmentation's cropped mask will be bitwise ANDed with the corresponding mask.
    - Python dtype: `Tuple[torch.Size, List[SEG]]`
    - Comfy dtype: `SEGS`
- **`masks`**
    - A collection of masks to apply to the segmentations. Each mask is applied to the corresponding segmentation in the `segs` collection.
    - Python dtype: `List[torch.Tensor]`
    - Comfy dtype: `MASK`
## Output types
- **`segs`**
    - The modified collection of segmentations after applying the masks. Each segmentation's mask is updated to reflect the bitwise AND operation with the corresponding mask.
    - Python dtype: `Tuple[torch.Size, List[SEG]]`
    - Comfy dtype: `SEGS`
## Usage tips
- Infra type: `GPU`
- Common nodes: unknown


## Source code
```python
class SegsBitwiseAndMaskForEach:
    @classmethod
    def INPUT_TYPES(s):
        return {"required": {
                        "segs": ("SEGS",),
                        "masks": ("MASK",),
                    }
                }

    RETURN_TYPES = ("SEGS",)
    FUNCTION = "doit"

    CATEGORY = "ImpactPack/Operation"

    def doit(self, segs, masks):
        return (core.apply_mask_to_each_seg(segs, masks), )

```
