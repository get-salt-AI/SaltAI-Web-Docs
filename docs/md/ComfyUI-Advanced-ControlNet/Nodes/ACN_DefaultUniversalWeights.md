---
tags:
- ControlNet
- Weight
---

# Default Weights 🛂🅐🅒🅝
## Documentation
- Class name: `ACN_DefaultUniversalWeights`
- Category: `Adv-ControlNet 🛂🅐🅒🅝/weights`
- Output node: `False`

This node is designed to dynamically adjust the weights for advanced control networks in image generation tasks. It allows for the customization of weight parameters to influence the generation process, providing flexibility in achieving desired visual outcomes.
## Input types
### Optional
- **`cn_extras`**
    - Allows for the inclusion of additional, custom control network weight parameters, enhancing the node's adaptability to specific needs.
    - Comfy dtype: `CN_WEIGHTS_EXTRAS`
    - Python dtype: `dict[str]`
## Output types
- **`CN_WEIGHTS`**
    - Comfy dtype: `CONTROL_NET_WEIGHTS`
    - The adjusted control network weights, ready for use in the image generation process.
    - Python dtype: `ControlWeights`
- **`TK_SHORTCUT`**
    - Comfy dtype: `TIMESTEP_KEYFRAME`
    - A keyframe group that encapsulates the timing and application of the control weights throughout the generation process.
    - Python dtype: `TimestepKeyframeGroup`
## Usage tips
- Infra type: `CPU`
- Common nodes: unknown


## Source code
```python
class DefaultWeights:
    @classmethod
    def INPUT_TYPES(s):
        return {
            "optional": {
                "cn_extras": ("CN_WEIGHTS_EXTRAS",),
            }
        }
    
    RETURN_TYPES = ("CONTROL_NET_WEIGHTS", "TIMESTEP_KEYFRAME",)
    RETURN_NAMES = WEIGHTS_RETURN_NAMES
    FUNCTION = "load_weights"

    CATEGORY = "Adv-ControlNet 🛂🅐🅒🅝/weights"

    def load_weights(self, cn_extras: dict[str]={}):
        weights = ControlWeights.default(extras=cn_extras)
        return (weights, TimestepKeyframeGroup.default(TimestepKeyframe(control_weights=weights))) 

```
