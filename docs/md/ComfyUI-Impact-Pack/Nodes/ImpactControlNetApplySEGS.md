# ControlNetApply (SEGS)
## Documentation
- Class name: `ImpactControlNetApplySEGS`
- Category: `ImpactPack/Util`
- Output node: `False`

This node applies a control network to a given set of segmentation masks (SEGS), adjusting them based on the control network's parameters and optionally a control image. It allows for dynamic modification of segmentation masks with varying degrees of strength, enabling fine-tuned adjustments or transformations.
## Input types
### Required
- **`segs`**
    - The segmentation masks to be modified. This is the primary input that the control network will adjust.
    - Python dtype: `Tuple[Any, List[SEG]]`
    - Comfy dtype: `SEGS`
- **`control_net`**
    - The control network to apply to the segmentation masks. It defines the transformation or adjustment to be performed.
    - Python dtype: `ControlNet`
    - Comfy dtype: `CONTROL_NET`
- **`strength`**
    - Determines the intensity of the control network's effect on the segmentation masks. A higher value results in more pronounced changes.
    - Python dtype: `float`
    - Comfy dtype: `FLOAT`
### Optional
- **`segs_preprocessor`**
    - An optional preprocessor for the segmentation masks before applying the control network. It can be used for additional adjustments or preprocessing steps.
    - Python dtype: `Optional[SEGS_PREPROCESSOR]`
    - Comfy dtype: `SEGS_PREPROCESSOR`
- **`control_image`**
    - An optional control image that can influence the control network's adjustments on the segmentation masks. It provides an additional layer of control.
    - Python dtype: `Optional[torch.Tensor]`
    - Comfy dtype: `IMAGE`
## Output types
- **`segs`**
    - The modified segmentation masks after the control network's adjustments have been applied.
    - Python dtype: `Tuple[Any, List[SEG]]`
    - Comfy dtype: `SEGS`
## Usage tips
- Infra type: `GPU`
- Common nodes: `DetailerForEachDebugPipe,ImpactControlNetApplySEGS`


## Source code
```python
class ControlNetApplySEGS:
    @classmethod
    def INPUT_TYPES(s):
        return {"required": {
                    "segs": ("SEGS",),
                    "control_net": ("CONTROL_NET",),
                    "strength": ("FLOAT", {"default": 1.0, "min": 0.0, "max": 10.0, "step": 0.01}),
                    },
                "optional": {
                    "segs_preprocessor": ("SEGS_PREPROCESSOR",),
                    "control_image": ("IMAGE",)
                    }
                }

    RETURN_TYPES = ("SEGS",)
    FUNCTION = "doit"

    CATEGORY = "ImpactPack/Util"

    def doit(self, segs, control_net, strength, segs_preprocessor=None, control_image=None):
        new_segs = []

        for seg in segs[1]:
            control_net_wrapper = core.ControlNetWrapper(control_net, strength, segs_preprocessor, seg.control_net_wrapper,
                                                         original_size=segs[0], crop_region=seg.crop_region, control_image=control_image)
            new_seg = SEG(seg.cropped_image, seg.cropped_mask, seg.confidence, seg.crop_region, seg.bbox, seg.label, control_net_wrapper)
            new_segs.append(new_seg)

        return ((segs[0], new_segs), )

```
