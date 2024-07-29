---
tags:
- ControlNet
- ControlNetLoader
---

# T2IAdapter Soft Weights 🛂🅐🅒🅝
## Documentation
- Class name: `SoftT2IAdapterWeights`
- Category: `Adv-ControlNet 🛂🅐🅒🅝/weights/T2IAdapter`
- Output node: `False`

This node is designed to adapt and apply a set of weights specifically tailored for text-to-image (T2I) generation tasks within the Advanced ControlNet framework. It focuses on adjusting and optimizing the influence of various control weights based on provided parameters, enhancing the generation process by aligning it more closely with user-defined criteria.
## Input types
### Required
- **`weight_i`**
    - Specifies one of the weights for the T2I generation process, contributing to the fine-tuning of the T2I generation's control dynamics. The index 'i' ranges from 0 to 3, each representing a specific position in the sequence of weights.
    - Comfy dtype: `FLOAT`
    - Python dtype: `float`
- **`flip_weights`**
    - A boolean flag that, when true, reverses the order of weights to accommodate recent changes in the ComfyUI framework.
    - Comfy dtype: `BOOLEAN`
    - Python dtype: `bool`
### Optional
- **`uncond_multiplier`**
    - A multiplier applied unconditionally to all weights, offering a global scaling factor to adjust their overall impact.
    - Comfy dtype: `FLOAT`
    - Python dtype: `float`
- **`cn_extras`**
    - An optional dictionary of extra parameters for further customization of the control net weights.
    - Comfy dtype: `CN_WEIGHTS_EXTRAS`
    - Python dtype: `dict[str]`
## Output types
- **`CN_WEIGHTS`**
    - Comfy dtype: `CONTROL_NET_WEIGHTS`
    - The adapted set of control net weights, ready for use in the T2I generation process.
    - Python dtype: `ControlWeights`
- **`TK_SHORTCUT`**
    - Comfy dtype: `TIMESTEP_KEYFRAME`
    - A keyframe group representing the temporal distribution of control weights over the generation timeline.
    - Python dtype: `TimestepKeyframeGroup`
## Usage tips
- Infra type: `CPU`
- Common nodes: unknown


## Source code
```python
class SoftT2IAdapterWeights:
    @classmethod
    def INPUT_TYPES(s):
        return {
            "required": {
                "weight_00": ("FLOAT", {"default": 0.25, "min": 0.0, "max": 10.0, "step": 0.001}, ),
                "weight_01": ("FLOAT", {"default": 0.62, "min": 0.0, "max": 10.0, "step": 0.001}, ),
                "weight_02": ("FLOAT", {"default": 0.825, "min": 0.0, "max": 10.0, "step": 0.001}, ),
                "weight_03": ("FLOAT", {"default": 1.0, "min": 0.0, "max": 10.0, "step": 0.001}, ),
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

    CATEGORY = "Adv-ControlNet 🛂🅐🅒🅝/weights/T2IAdapter"

    def load_weights(self, weight_00, weight_01, weight_02, weight_03, flip_weights,
                     uncond_multiplier: float=1.0, cn_extras: dict[str]={}):
        weights = [weight_00, weight_01, weight_02, weight_03]
        weights = get_properly_arranged_t2i_weights(weights)
        weights.reverse()  # to account for recent ComfyUI changes
        weights = ControlWeights.t2iadapter(weights, flip_weights=flip_weights, uncond_multiplier=uncond_multiplier, extras=cn_extras)
        return (weights, TimestepKeyframeGroup.default(TimestepKeyframe(control_weights=weights)))

```
