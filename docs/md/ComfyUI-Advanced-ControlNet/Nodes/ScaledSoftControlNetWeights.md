---
tags:
- ControlNet
- ControlNetLoader
---

# Scaled Soft Weights 🛂🅐🅒🅝
## Documentation
- Class name: `ScaledSoftControlNetWeights`
- Category: `Adv-ControlNet 🛂🅐🅒🅝/weights`
- Output node: `False`

The ScaledSoftControlNetWeights node is designed to dynamically adjust the weights used in a control network based on a set of input parameters. It allows for the fine-tuning of the control network's behavior by scaling the base multiplier, optionally flipping the weights, and adjusting for unconditional multipliers and additional control network-specific parameters.
## Input types
### Required
- **`base_multiplier`**
    - Specifies the base scaling factor for the control network weights, serving as the foundational adjustment to the control network's behavior.
    - Comfy dtype: `FLOAT`
    - Python dtype: `float`
- **`flip_weights`**
    - A boolean flag that, when set to True, reverses the effect of the weights, allowing for inverted control behavior in the network.
    - Comfy dtype: `BOOLEAN`
    - Python dtype: `bool`
### Optional
- **`uncond_multiplier`**
    - Defines an unconditional multiplier applied to the weights, offering a way to adjust the influence of the control network globally without specific conditionality.
    - Comfy dtype: `FLOAT`
    - Python dtype: `float`
- **`cn_extras`**
    - A dictionary of additional, network-specific parameters that can be used to further customize the control network's behavior.
    - Comfy dtype: `CN_WEIGHTS_EXTRAS`
    - Python dtype: `dict[str]`
## Output types
- **`CN_WEIGHTS`**
    - Comfy dtype: `CONTROL_NET_WEIGHTS`
    - The adjusted weights for the control network, reflecting the applied scaling, flipping, and unconditional adjustments.
    - Python dtype: `ControlWeights`
- **`TK_SHORTCUT`**
    - Comfy dtype: `TIMESTEP_KEYFRAME`
    - A keyframe group representing the temporal application of the adjusted control network weights over time.
    - Python dtype: `TimestepKeyframeGroup`
## Usage tips
- Infra type: `CPU`
- Common nodes:
    - [ControlNetLoaderAdvanced](../../ComfyUI-Advanced-ControlNet/Nodes/ControlNetLoaderAdvanced.md)



## Source code
```python
class ScaledSoftUniversalWeights:
    @classmethod
    def INPUT_TYPES(s):
        return {
            "required": {
                "base_multiplier": ("FLOAT", {"default": 0.825, "min": 0.0, "max": 1.0, "step": 0.001}, ),
                "flip_weights": ("BOOLEAN", {"default": False}),
            },
            "optional": {
                "uncond_multiplier": ("FLOAT", {"default": 1.0, "min": 0.0, "max": 1.0, "step": 0.01}, ),
                "cn_extras": ("CN_WEIGHTS_EXTRAS",),
            }
        }
    
    RETURN_TYPES = ("CONTROL_NET_WEIGHTS", "TIMESTEP_KEYFRAME",)
    RETURN_NAMES = WEIGHTS_RETURN_NAMES
    FUNCTION = "load_weights"

    CATEGORY = "Adv-ControlNet 🛂🅐🅒🅝/weights"

    def load_weights(self, base_multiplier, flip_weights, uncond_multiplier: float=1.0, cn_extras: dict[str]={}):
        weights = ControlWeights.universal(base_multiplier=base_multiplier, flip_weights=flip_weights, uncond_multiplier=uncond_multiplier, extras=cn_extras)
        return (weights, TimestepKeyframeGroup.default(TimestepKeyframe(control_weights=weights))) 

```
