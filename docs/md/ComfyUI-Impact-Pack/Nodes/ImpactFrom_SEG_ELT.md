# From SEG_ELT
## Documentation
- Class name: `ImpactFrom_SEG_ELT`
- Category: `ImpactPack/Util`
- Output node: `False`

The `ImpactFrom_SEG_ELT` node processes a segment element (`SEG_ELT`) to extract various properties and representations, including the original segment, a cropped image and mask, crop region, bounding box, control network wrapper, confidence score, and label. This node is essential for further processing or analysis of segment elements within the ImpactPack framework.
## Input types
### Required
- **`seg_elt`**
    - The segment element to be processed. It is crucial for extracting detailed information and representations for further analysis.
    - Python dtype: `SEG_ELT`
    - Comfy dtype: `SEG_ELT`
## Output types
- **`seg_elt`**
    - Returns the original segment element.
    - Python dtype: `SEG_ELT`
    - Comfy dtype: `SEG_ELT`
- **`image`**
    - The cropped image extracted from the segment element.
    - Python dtype: `Optional[torch.Tensor]`
    - Comfy dtype: `IMAGE`
- **`mask`**
    - The cropped mask corresponding to the segment element.
    - Python dtype: `torch.Tensor`
    - Comfy dtype: `MASK`
- **`seg_elt_crop_region`**
    - The crop region used for extracting the cropped image and mask.
    - Python dtype: `Tuple[int, int, int, int]`
    - Comfy dtype: `SEG_ELT_crop_region`
- **`seg_elt_bbox`**
    - The bounding box of the segment element.
    - Python dtype: `Tuple[int, int, int, int]`
    - Comfy dtype: `SEG_ELT_bbox`
- **`seg_elt_control_net_wrapper`**
    - A wrapper for the control network associated with the segment element.
    - Python dtype: `Any`
    - Comfy dtype: `SEG_ELT_control_net_wrapper`
- **`float`**
    - The confidence score of the segment element.
    - Python dtype: `float`
    - Comfy dtype: `FLOAT`
- **`string`**
    - The label assigned to the segment element.
    - Python dtype: `str`
    - Comfy dtype: `STRING`
## Usage tips
- Infra type: `CPU`
- Common nodes: unknown


## Source code
```python
class From_SEG_ELT:
    @classmethod
    def INPUT_TYPES(s):
        return {"required": {
                     "seg_elt": ("SEG_ELT", ),
                     },
                }

    RETURN_TYPES = ("SEG_ELT", "IMAGE", "MASK", "SEG_ELT_crop_region", "SEG_ELT_bbox", "SEG_ELT_control_net_wrapper", "FLOAT", "STRING")
    RETURN_NAMES = ("seg_elt", "cropped_image", "cropped_mask", "crop_region", "bbox", "control_net_wrapper", "confidence", "label")

    FUNCTION = "doit"

    CATEGORY = "ImpactPack/Util"

    def doit(self, seg_elt):
        cropped_image = to_tensor(seg_elt.cropped_image) if seg_elt.cropped_image is not None else None
        return (seg_elt, cropped_image, to_tensor(seg_elt.cropped_mask), seg_elt.crop_region, seg_elt.bbox, seg_elt.control_net_wrapper, seg_elt.confidence, seg_elt.label,)

```
