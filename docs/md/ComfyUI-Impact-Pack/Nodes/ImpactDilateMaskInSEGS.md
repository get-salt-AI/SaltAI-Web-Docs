# Dilate Mask (SEGS)
## Documentation
- Class name: `ImpactDilateMaskInSEGS`
- Category: `ImpactPack/Util`
- Output node: `False`

This node applies a dilation operation to the masks of segmentation elements (SEGS) provided as input. The dilation process enlarges the white (or foreground) regions in each mask by a specified dilation factor, which can be positive for dilation or negative for erosion, effectively modifying the segmentation boundaries.
## Input types
### Required
- **`segs`**
    - The segmentation elements (SEGS) to which the dilation operation will be applied. This input is crucial for defining the segmentation areas to be modified.
    - Python dtype: `Tuple[str, List[SEG]]`
    - Comfy dtype: `SEGS`
- **`dilation`**
    - The factor by which the masks of the segmentation elements are to be dilated. A positive value enlarges the white regions, while a negative value performs erosion, shrinking the white regions.
    - Python dtype: `int`
    - Comfy dtype: `INT`
## Output types
- **`segs`**
    - The modified segmentation elements (SEGS) after applying the dilation operation to their masks. This output reflects the changes in segmentation boundaries due to dilation or erosion.
    - Python dtype: `Tuple[str, List[SEG]]`
    - Comfy dtype: `SEGS`
## Usage tips
- Infra type: `CPU`
- Common nodes: `ImpactDilateMaskInSEGS`


## Source code
```python
class DilateMaskInSEGS:
    @classmethod
    def INPUT_TYPES(s):
        return {"required": {
                     "segs": ("SEGS", ),
                     "dilation": ("INT", {"default": 10, "min": -512, "max": 512, "step": 1}),
                }}

    RETURN_TYPES = ("SEGS", )

    FUNCTION = "doit"

    CATEGORY = "ImpactPack/Util"

    def doit(self, segs, dilation):
        new_segs = []
        for seg in segs[1]:
            mask = core.dilate_mask(seg.cropped_mask, dilation)
            seg = SEG(seg.cropped_image, mask, seg.confidence, seg.crop_region, seg.bbox, seg.label, seg.control_net_wrapper)
            new_segs.append(seg)

        return ((segs[0], new_segs), )

```
