# Bitwise(SEGS - SEGS)
## Documentation
- Class name: `SubtractMaskForEach`
- Category: `ImpactPack/Operation`
- Output node: `False`

The `SubtractMaskForEach` node is designed to perform element-wise subtraction between pairs of masks for each frame or segment in a sequence. This operation is useful for removing common elements or reducing redundancy between consecutive frames or segments, enhancing the distinct features of each.
## Input types
### Required
- **`base_segs`**
    - The first set of segments to be subtracted from. It serves as the base in the subtraction operation, determining the initial state or features to be modified.
    - Python dtype: `List[torch.Tensor]`
    - Comfy dtype: `SEGS`
- **`mask_segs`**
    - The second set of segments to be subtracted. This represents the elements or features to be removed from the first set, effectively altering its composition.
    - Python dtype: `List[torch.Tensor]`
    - Comfy dtype: `SEGS`
## Output types
- **`segs`**
    - The resulting set of segments after subtracting `mask_segs` from `base_segs`. This emphasizes unique features or changes by highlighting the differences between the two input sets.
    - Python dtype: `List[torch.Tensor]`
    - Comfy dtype: `SEGS`
## Usage tips
- Infra type: `CPU`
- Common nodes: unknown


## Source code
```python
class SubtractMaskForEach:
    @classmethod
    def INPUT_TYPES(s):
        return {"required": {
                        "base_segs": ("SEGS",),
                        "mask_segs": ("SEGS",),
                    }
                }

    RETURN_TYPES = ("SEGS",)
    FUNCTION = "doit"

    CATEGORY = "ImpactPack/Operation"

    def doit(self, base_segs, mask_segs):

        result = []

        for bseg in base_segs[1]:
            cropped_mask1 = bseg.cropped_mask.copy()
            crop_region1 = bseg.crop_region

            for mseg in mask_segs[1]:
                cropped_mask2 = mseg.cropped_mask
                crop_region2 = mseg.crop_region

                # compute the intersection of the two crop regions
                intersect_region = (max(crop_region1[0], crop_region2[0]),
                                    max(crop_region1[1], crop_region2[1]),
                                    min(crop_region1[2], crop_region2[2]),
                                    min(crop_region1[3], crop_region2[3]))

                changed = False

                # subtract operation
                for i in range(intersect_region[0], intersect_region[2]):
                    for j in range(intersect_region[1], intersect_region[3]):
                        if cropped_mask1[j - crop_region1[1], i - crop_region1[0]] == 1 and \
                                cropped_mask2[j - crop_region2[1], i - crop_region2[0]] == 1:
                            # pixel overlaps with both masks, set it as 0
                            changed = True
                            cropped_mask1[j - crop_region1[1], i - crop_region1[0]] = 0
                        else:
                            # pixel does not overlap with both masks, don't care
                            pass

                if changed:
                    item = SEG(bseg.cropped_image, cropped_mask1, bseg.confidence, bseg.crop_region, bseg.bbox, bseg.label, None)
                    result.append(item)
                else:
                    result.append(base_segs)

        return ((base_segs[0], result),)

```
