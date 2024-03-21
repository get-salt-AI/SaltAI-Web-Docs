# Dilate Mask (SEG_ELT)
## Documentation
- Class name: `ImpactDilate_Mask_SEG_ELT`
- Category: `ImpactPack/Util`
- Output node: `False`

This node applies dilation or erosion to the mask of a single segmentation element (SEG_ELT), based on the specified dilation factor. Dilation increases the size of the mask's features, while erosion reduces them. This operation is useful for adjusting the boundaries of segmentation masks for various image processing tasks.
## Input types
### Required
- **`seg_elt`**
    - The segmentation element (SEG_ELT) whose mask will be modified. This is the primary input for the operation.
    - Python dtype: `SEG_ELT`
    - Comfy dtype: `SEG_ELT`
- **`dilation`**
    - The factor by which the mask will be dilated or eroded. Positive values cause dilation, increasing the mask size, while negative values cause erosion, reducing the mask size.
    - Python dtype: `int`
    - Comfy dtype: `INT`
## Output types
- **`seg_elt`**
    - The modified segmentation element (SEG_ELT) with the mask either dilated or eroded, depending on the dilation factor.
    - Python dtype: `SEG_ELT`
    - Comfy dtype: `SEG_ELT`
## Usage tips
- Infra type: `CPU`
- Common nodes: unknown


## Source code
```python
class Dilate_SEG_ELT:
    @classmethod
    def INPUT_TYPES(s):
        return {"required": {
                     "seg_elt": ("SEG_ELT", ),
                     "dilation": ("INT", {"default": 10, "min": -512, "max": 512, "step": 1}),
                }}

    RETURN_TYPES = ("SEG_ELT", )

    FUNCTION = "doit"

    CATEGORY = "ImpactPack/Util"

    def doit(self, seg, dilation):
        mask = core.dilate_mask(seg.cropped_mask, dilation)
        seg = SEG(seg.cropped_image, mask, seg.confidence, seg.crop_region, seg.bbox, seg.label, seg.control_net_wrapper)
        return (seg,)

```
