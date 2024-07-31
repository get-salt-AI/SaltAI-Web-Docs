---
tags:
- ControlNet
- Weight
---

# Scaled Soft Masked Weights 🛂🅐🅒🅝
## Documentation
- Class name: `ScaledSoftMaskedUniversalWeights`
- Category: `Adv-ControlNet 🛂🅐🅒🅝/weights`
- Output node: `False`

The ScaledSoftMaskedUniversalWeights node is designed to dynamically adjust the weights used in advanced control networks based on input parameters. It focuses on applying scaling and optional flipping to the base weights, incorporating an unconditional multiplier for further customization, and allowing for additional weight adjustments through extras. This node is essential for tailoring the behavior of control networks to specific needs, enhancing flexibility and control over the generated outputs.
## Input types
### Required
- **`mask`**
    - Specifies the input mask used to dynamically scale the weights based on spatial or other criteria, providing a mechanism for context-sensitive weight adjustments.
    - Comfy dtype: `MASK`
    - Python dtype: `Tensor`
- **`min_base_multiplier`**
    - Sets the minimum scaling factor for the weights, allowing for a lower bound on weight adjustments. This parameter is crucial for defining the range of weight scaling.
    - Comfy dtype: `FLOAT`
    - Python dtype: `float`
- **`max_base_multiplier`**
    - Sets the maximum scaling factor for the weights, establishing an upper limit on weight adjustments. This parameter is essential for defining the range of weight scaling.
    - Comfy dtype: `FLOAT`
    - Python dtype: `float`
### Optional
- **`uncond_multiplier`**
    - Applies an unconditional multiplier to the weights, offering a method to uniformly scale the weights regardless of other conditions. This parameter enhances the node's ability to fine-tune the control network's response.
    - Comfy dtype: `FLOAT`
    - Python dtype: `float`
- **`cn_extras`**
    - Allows for the inclusion of extra parameters for further customization of the weights, providing a means to introduce specific adjustments beyond the standard options.
    - Comfy dtype: `CN_WEIGHTS_EXTRAS`
    - Python dtype: `dict[str]`
## Output types
- **`CN_WEIGHTS`**
    - Comfy dtype: `CONTROL_NET_WEIGHTS`
    - Represents the adjusted weights for the control network, reflecting the combined effects of the base multiplier, flip option, unconditional multiplier, and any extras.
    - Python dtype: `ControlWeights`
- **`TK_SHORTCUT`**
    - Comfy dtype: `TIMESTEP_KEYFRAME`
    - Encapsulates the adjusted weights within a timestep keyframe structure, facilitating their integration into the control network's temporal dynamics.
    - Python dtype: `TimestepKeyframeGroup`
## Usage tips
- Infra type: `CPU`
- Common nodes: unknown


## Source code
```python
class ScaledSoftMaskedUniversalWeights:
    @classmethod
    def INPUT_TYPES(s):
        return {
            "required": {
                "mask": ("MASK", ),
                "min_base_multiplier": ("FLOAT", {"default": 0.0, "min": 0.0, "max": 1.0, "step": 0.001}, ),
                "max_base_multiplier": ("FLOAT", {"default": 1.0, "min": 0.0, "max": 1.0, "step": 0.001}, ),
                #"lock_min": ("BOOLEAN", {"default": False}, ),
                #"lock_max": ("BOOLEAN", {"default": False}, ),
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

    def load_weights(self, mask: Tensor, min_base_multiplier: float, max_base_multiplier: float, lock_min=False, lock_max=False,
                     uncond_multiplier: float=1.0, cn_extras: dict[str]={}):
        # normalize mask
        mask = mask.clone()
        x_min = 0.0 if lock_min else mask.min()
        x_max = 1.0 if lock_max else mask.max()
        if x_min == x_max:
            mask = torch.ones_like(mask) * max_base_multiplier
        else:
            mask = linear_conversion(mask, x_min, x_max, min_base_multiplier, max_base_multiplier)
        weights = ControlWeights.universal_mask(weight_mask=mask, uncond_multiplier=uncond_multiplier, extras=cn_extras)
        return (weights, TimestepKeyframeGroup.default(TimestepKeyframe(control_weights=weights)))

```
