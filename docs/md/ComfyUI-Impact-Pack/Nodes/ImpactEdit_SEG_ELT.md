# Edit SEG_ELT
## Documentation
- Class name: `ImpactEdit_SEG_ELT`
- Category: `ImpactPack/Util`
- Output node: `False`

This node facilitates the editing or modification of SEG_ELT objects, enabling operations such as scaling the bounding box, adjusting the segmentation mask, and updating metadata like confidence or labels. It's particularly useful for refining segmentation results for further analysis or processing.
## Input types
### Required
- **`seg_elt`**
    - unknown
    - Python dtype: `unknown`
    - Comfy dtype: `SEG_ELT`
### Optional
- **`cropped_image_opt`**
    - Optional. An alternative cropped image to replace the original in the SEG_ELT object.
    - Python dtype: `Optional[Image]`
    - Comfy dtype: `IMAGE`
- **`cropped_mask_opt`**
    - Optional. An alternative segmentation mask to replace the original in the SEG_ELT object.
    - Python dtype: `Optional[Mask]`
    - Comfy dtype: `MASK`
- **`crop_region_opt`**
    - Optional. A new crop region to replace the original in the SEG_ELT object.
    - Python dtype: `Optional[Tuple[int, int, int, int]]`
    - Comfy dtype: `SEG_ELT_crop_region`
- **`bbox_opt`**
    - Optional. A new bounding box to replace the original in the SEG_ELT object.
    - Python dtype: `Optional[Tuple[int, int, int, int]]`
    - Comfy dtype: `SEG_ELT_bbox`
- **`control_net_wrapper_opt`**
    - Optional. A new control network wrapper to replace the original in the SEG_ELT object.
    - Python dtype: `Optional[ControlNetWrapper]`
    - Comfy dtype: `SEG_ELT_control_net_wrapper`
- **`confidence_opt`**
    - Optional. A new confidence value to replace the original in the SEG_ELT object. This parameter must be explicitly provided by the user.
    - Python dtype: `Optional[float]`
    - Comfy dtype: `FLOAT`
- **`label_opt`**
    - Optional. A new label to replace the original in the SEG_ELT object. This parameter must be explicitly provided by the user.
    - Python dtype: `Optional[str]`
    - Comfy dtype: `STRING`
## Output types
- **`seg_elt`**
    - The edited SEG_ELT object, updated according to the provided optional parameters.
    - Python dtype: `SEG_ELT`
    - Comfy dtype: `SEG_ELT`
## Usage tips
- Infra type: `CPU`
- Common nodes: unknown


## Source code
```python
class Edit_SEG_ELT:
    @classmethod
    def INPUT_TYPES(s):
        return {"required": {
                     "seg_elt": ("SEG_ELT", ),
                     },
                "optional": {
                     "cropped_image_opt": ("IMAGE", ),
                     "cropped_mask_opt": ("MASK", ),
                     "crop_region_opt": ("SEG_ELT_crop_region", ),
                     "bbox_opt": ("SEG_ELT_bbox", ),
                     "control_net_wrapper_opt": ("SEG_ELT_control_net_wrapper", ),
                     "confidence_opt": ("FLOAT", {"min": 0, "max": 1.0, "step": 0.1, "forceInput": True}),
                     "label_opt": ("STRING", {"multiline": False, "forceInput": True}),
                    }
                }

    RETURN_TYPES = ("SEG_ELT", )

    FUNCTION = "doit"

    CATEGORY = "ImpactPack/Util"

    def doit(self, seg_elt, cropped_image_opt=None, cropped_mask_opt=None, confidence_opt=None, crop_region_opt=None,
             bbox_opt=None, label_opt=None, control_net_wrapper_opt=None):

        cropped_image = seg_elt.cropped_image if cropped_image_opt is None else cropped_image_opt
        cropped_mask = seg_elt.cropped_mask if cropped_mask_opt is None else cropped_mask_opt
        confidence = seg_elt.confidence if confidence_opt is None else confidence_opt
        crop_region = seg_elt.crop_region if crop_region_opt is None else crop_region_opt
        bbox = seg_elt.bbox if bbox_opt is None else bbox_opt
        label = seg_elt.label if label_opt is None else label_opt
        control_net_wrapper = seg_elt.control_net_wrapper if control_net_wrapper_opt is None else control_net_wrapper_opt

        cropped_image = cropped_image.numpy() if cropped_image is not None else None

        if isinstance(cropped_mask, torch.Tensor):
            if len(cropped_mask.shape) == 3:
                cropped_mask = cropped_mask.squeeze(0)

            cropped_mask = cropped_mask.numpy()

        seg = SEG(cropped_image, cropped_mask, confidence, crop_region, bbox, label, control_net_wrapper)

        return (seg,)

```
