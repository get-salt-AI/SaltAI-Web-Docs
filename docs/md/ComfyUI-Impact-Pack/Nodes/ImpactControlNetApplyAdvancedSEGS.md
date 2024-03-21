# ControlNetApplyAdvanced (SEGS)
## Documentation
- Class name: `ImpactControlNetApplyAdvancedSEGS`
- Category: `ImpactPack/Util`
- Output node: `False`

This node applies an advanced control network to a set of segmentation masks (SEGS) with additional parameters for fine-tuning the effect. It allows for the dynamic adjustment of the control network's influence over the segmentation masks based on specified strength and percentage range, potentially using a preprocessor or a control image for enhanced customization.
## Input types
### Required
- **`segs`**
    - The segmentation masks to which the control network will be applied. This is the primary input for transformation.
    - Python dtype: `Tuple[torch.Tensor, List[SEG]]`
    - Comfy dtype: `SEGS`
- **`control_net`**
    - The control network model used to modify the segmentation masks.
    - Python dtype: `torch.nn.Module`
    - Comfy dtype: `CONTROL_NET`
- **`strength`**
    - Determines the intensity of the control network's effect on the segmentation masks.
    - Python dtype: `float`
    - Comfy dtype: `FLOAT`
- **`start_percent`**
    - The starting percentage of the control network's effect, allowing for gradual application.
    - Python dtype: `float`
    - Comfy dtype: `FLOAT`
- **`end_percent`**
    - The ending percentage of the control network's effect, enabling fine-tuning of the application.
    - Python dtype: `float`
    - Comfy dtype: `FLOAT`
### Optional
- **`segs_preprocessor`**
    - An optional preprocessor for the segmentation masks before applying the control network.
    - Python dtype: `Optional[Callable]`
    - Comfy dtype: `SEGS_PREPROCESSOR`
- **`control_image`**
    - An optional control image to guide the control network's application.
    - Python dtype: `Optional[torch.Tensor]`
    - Comfy dtype: `IMAGE`
## Output types
- **`segs`**
    - The transformed segmentation masks after the control network's application.
    - Python dtype: `Tuple[torch.Tensor, List[SEG]]`
    - Comfy dtype: `SEGS`
## Usage tips
- Infra type: `GPU`
- Common nodes: unknown


## Source code
```python
class ControlNetApplyAdvancedSEGS:
    @classmethod
    def INPUT_TYPES(s):
        return {"required": {
                    "segs": ("SEGS",),
                    "control_net": ("CONTROL_NET",),
                    "strength": ("FLOAT", {"default": 1.0, "min": 0.0, "max": 10.0, "step": 0.01}),
                    "start_percent": ("FLOAT", {"default": 0.0, "min": 0.0, "max": 1.0, "step": 0.001}),
                    "end_percent": ("FLOAT", {"default": 1.0, "min": 0.0, "max": 1.0, "step": 0.001})
                    },
                "optional": {
                    "segs_preprocessor": ("SEGS_PREPROCESSOR",),
                    "control_image": ("IMAGE",)
                    }
                }

    RETURN_TYPES = ("SEGS",)
    FUNCTION = "doit"

    CATEGORY = "ImpactPack/Util"

    def doit(self, segs, control_net, strength, start_percent, end_percent, segs_preprocessor=None, control_image=None):
        new_segs = []

        for seg in segs[1]:
            control_net_wrapper = core.ControlNetAdvancedWrapper(control_net, strength, start_percent, end_percent, segs_preprocessor,
                                                                 seg.control_net_wrapper, original_size=segs[0], crop_region=seg.crop_region,
                                                                 control_image=control_image)
            new_seg = SEG(seg.cropped_image, seg.cropped_mask, seg.confidence, seg.crop_region, seg.bbox, seg.label, control_net_wrapper)
            new_segs.append(new_seg)

        return ((segs[0], new_segs), )

```
