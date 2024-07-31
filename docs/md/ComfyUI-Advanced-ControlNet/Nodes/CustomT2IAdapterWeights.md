---
tags:
- ControlNet
- Weight
---

# T2IAdapter Custom Weights 🛂🅐🅒🅝
## Documentation
- Class name: `CustomT2IAdapterWeights`
- Category: `Adv-ControlNet 🛂🅐🅒🅝/weights/T2IAdapter`
- Output node: `False`

The CustomT2IAdapterWeights node is designed for advanced control over text-to-image (T2I) generation processes by allowing users to dynamically adjust the weights of different control parameters. It integrates with the ComfyUI Advanced ControlNet framework to provide a flexible mechanism for fine-tuning image generation based on specific weights, including the ability to flip weights and apply an unconditional multiplier for nuanced control.
## Input types
### Required
- **`weight_i`**
    - Represents a series of weights (weight_00 to weight_03) for controlling various aspects of the T2I generation process. Each weight influences the impact of corresponding control parameters on the image generation outcome.
    - Comfy dtype: `FLOAT`
    - Python dtype: `float`
- **`flip_weights`**
    - A boolean flag to reverse the order of weights, accommodating recent changes in ComfyUI's handling of control parameters.
    - Comfy dtype: `BOOLEAN`
    - Python dtype: `bool`
### Optional
- **`uncond_multiplier`**
    - Applies an unconditional multiplier to all weights, offering an additional layer of control over the image generation process.
    - Comfy dtype: `FLOAT`
    - Python dtype: `float`
- **`cn_extras`**
    - Allows for the inclusion of extra control parameters specific to the ComfyUI Advanced ControlNet, enhancing customization.
    - Comfy dtype: `CN_WEIGHTS_EXTRAS`
    - Python dtype: `dict[str]`
## Output types
- **`CN_WEIGHTS`**
    - Comfy dtype: `CONTROL_NET_WEIGHTS`
    - The adjusted control net weights, ready for use in T2I generation.
    - Python dtype: `list[float]`
- **`TK_SHORTCUT`**
    - Comfy dtype: `TIMESTEP_KEYFRAME`
    - A keyframe group object encapsulating the control weights for each timestep, facilitating precise control over the image generation timeline.
    - Python dtype: `TimestepKeyframeGroup`
## Usage tips
- Infra type: `CPU`
- Common nodes: unknown


## Source code
```python
class CustomT2IAdapterWeights:
    @classmethod
    def INPUT_TYPES(s):
        return {
            "required": {
                "weight_00": ("FLOAT", {"default": 1.0, "min": 0.0, "max": 10.0, "step": 0.001}, ),
                "weight_01": ("FLOAT", {"default": 1.0, "min": 0.0, "max": 10.0, "step": 0.001}, ),
                "weight_02": ("FLOAT", {"default": 1.0, "min": 0.0, "max": 10.0, "step": 0.001}, ),
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
