# Bitwise(SEGS & SEGS)
## Documentation
- Class name: `SegsBitwiseAndMaskForEach`
- Category: `ImpactPack/Operation`
- Output node: `False`

This node applies a bitwise AND operation between each segmentation in a collection and a set of masks, producing a new set of segmentations where each segmentation is masked individually. It's particularly useful for applying specific mask operations to each segmentation in a dataset, allowing for fine-grained control over the output.
## Input types
### Required
- **`base_segs`**
    - unknown
    - Python dtype: `unknown`
    - Comfy dtype: `SEGS`
- **`mask_segs`**
    - unknown
    - Python dtype: `unknown`
    - Comfy dtype: `SEGS`
## Output types
- **`segs`**
    - The resulting collection of segmentations after applying the bitwise AND operation with the masks. Each segmentation in the input is masked by its corresponding mask, producing a modified set of segmentations.
    - Python dtype: `List[torch.Tensor]`
    - Comfy dtype: `SEGS`
## Usage tips
- Infra type: `GPU`
- Common nodes: unknown


## Source code
```python
class BitwiseAndMaskForEach:
    @classmethod
    def INPUT_TYPES(s):
        return {"required":
            {
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

                overlapped = False

                # set all pixels in cropped_mask1 to 0 except for those that overlap with cropped_mask2
                for i in range(intersect_region[0], intersect_region[2]):
                    for j in range(intersect_region[1], intersect_region[3]):
                        if cropped_mask1[j - crop_region1[1], i - crop_region1[0]] == 1 and \
                                cropped_mask2[j - crop_region2[1], i - crop_region2[0]] == 1:
                            # pixel overlaps with both masks, keep it as 1
                            overlapped = True
                            pass
                        else:
                            # pixel does not overlap with both masks, set it to 0
                            cropped_mask1[j - crop_region1[1], i - crop_region1[0]] = 0

                if overlapped:
                    item = SEG(bseg.cropped_image, cropped_mask1, bseg.confidence, bseg.crop_region, bseg.bbox, bseg.label, None)
                    result.append(item)

        return ((base_segs[0], result),)

```
