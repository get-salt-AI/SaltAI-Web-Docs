# ScaleBy BBOX (SEG_ELT)
## Documentation
- Class name: `ImpactScaleBy_BBOX_SEG_ELT`
- Category: `ImpactPack/Util`
- Output node: `False`

This node scales the bounding box of a segmentation element (SEG_ELT) by a specified factor, adjusts the segmentation mask accordingly, and fills the area outside the new bounding box with zeros. It's useful for focusing on or excluding specific parts of an image based on segmentation.
## Input types
### Required
- **`seg`**
    - The segmentation element to be scaled. It includes the original cropped image, segmentation mask, bounding box, and other metadata.
    - Python dtype: `SEG_ELT`
    - Comfy dtype: `SEG_ELT`
- **`scale_by`**
    - The factor by which the bounding box is scaled. A value greater than 1 enlarges the bounding box, while a value less than 1 shrinks it.
    - Python dtype: `float`
    - Comfy dtype: `FLOAT`
## Output types
- **`seg_elt`**
    - The scaled segmentation element, including the adjusted bounding box and segmentation mask.
    - Python dtype: `SEG_ELT`
    - Comfy dtype: `SEG_ELT`
## Usage tips
- Infra type: `CPU`
- Common nodes: unknown


## Source code
```python
class SEG_ELT_BBOX_ScaleBy:
    @classmethod
    def INPUT_TYPES(s):
        return {"required": {
                     "seg": ("SEG_ELT", ),
                     "scale_by": ("FLOAT", {"default": 1.0, "min": 0.01, "max": 8.0, "step": 0.01}), }
                }

    RETURN_TYPES = ("SEG_ELT", )

    FUNCTION = "doit"

    CATEGORY = "ImpactPack/Util"

    @staticmethod
    def fill_zero_outside_bbox(mask, crop_region, bbox):
        cx1, cy1, _, _ = crop_region
        x1, y1, x2, y2 = bbox
        x1, y1, x2, y2 = x1-cx1, y1-cy1, x2-cx1, y2-cy1
        h, w = mask.shape

        x1 = min(w-1, max(0, x1))
        x2 = min(w-1, max(0, x2))
        y1 = min(h-1, max(0, y1))
        y2 = min(h-1, max(0, y2))

        mask_cropped = mask.copy()
        mask_cropped[:, :x1] = 0  # zero fill left side
        mask_cropped[:, x2:] = 0  # zero fill right side
        mask_cropped[:y1, :] = 0  # zero fill top side
        mask_cropped[y2:, :] = 0  # zero fill bottom side
        return mask_cropped

    def doit(self, seg, scale_by):
        x1, y1, x2, y2 = seg.bbox
        w = x2-x1
        h = y2-y1

        dw = int((w * scale_by - w)/2)
        dh = int((h * scale_by - h)/2)

        bbox = (x1-dw, y1-dh, x2+dw, y2+dh)

        cropped_mask = SEG_ELT_BBOX_ScaleBy.fill_zero_outside_bbox(seg.cropped_mask, seg.crop_region, bbox)
        seg = SEG(seg.cropped_image, cropped_mask, seg.confidence, seg.crop_region, bbox, seg.label, seg.control_net_wrapper)
        return (seg,)

```
