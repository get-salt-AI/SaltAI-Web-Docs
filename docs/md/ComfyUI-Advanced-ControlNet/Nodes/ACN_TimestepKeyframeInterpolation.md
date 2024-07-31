---
tags:
- AnimationScheduling
- Frame
- Keyframe
---

# Timestep Keyframe Interpolation 🛂🅐🅒🅝
## Documentation
- Class name: `ACN_TimestepKeyframeInterpolation`
- Category: `Adv-ControlNet 🛂🅐🅒🅝/keyframes`
- Output node: `False`

The ACN_TimestepKeyframeInterpolation node is designed to generate a sequence of interpolated keyframes based on specified start and end percentages, strengths, and other control parameters. It leverages linear or custom interpolation methods to create smooth transitions between keyframes, facilitating the creation of dynamic and nuanced animations or effects within a control network.
## Input types
### Required
- **`start_percent`**
    - Specifies the starting percentage for the interpolation, marking the beginning of the transition sequence.
    - Comfy dtype: `FLOAT`
    - Python dtype: `float`
- **`end_percent`**
    - Defines the ending percentage for the interpolation, indicating the conclusion of the transition sequence.
    - Comfy dtype: `FLOAT`
    - Python dtype: `float`
- **`strength_start`**
    - Sets the initial strength value at the start of the interpolation, influencing the intensity or impact of the starting keyframe.
    - Comfy dtype: `FLOAT`
    - Python dtype: `float`
- **`strength_end`**
    - Determines the final strength value at the end of the interpolation, affecting the intensity or impact of the ending keyframe.
    - Comfy dtype: `FLOAT`
    - Python dtype: `float`
- **`interpolation`**
    - The method of interpolation to be used for transitioning between keyframes, such as linear, ease-in, ease-out, or custom methods.
    - Comfy dtype: `COMBO[STRING]`
    - Python dtype: `str`
- **`intervals`**
    - The number of intervals or steps between the start and end points, dictating the granularity of the interpolation.
    - Comfy dtype: `INT`
    - Python dtype: `int`
### Optional
- **`prev_timestep_kf`**
    - The previous TimestepKeyframeGroup to which the new interpolated keyframes will be added, allowing for the continuation or extension of an existing sequence.
    - Comfy dtype: `TIMESTEP_KEYFRAME`
    - Python dtype: `TimestepKeyframeGroup`
- **`cn_weights`**
    - Control network weights that influence the behavior and characteristics of the generated keyframes.
    - Comfy dtype: `CONTROL_NET_WEIGHTS`
    - Python dtype: `ControlWeights`
- **`latent_keyframe`**
    - A group of latent keyframes that can be optionally included to further customize the interpolation process.
    - Comfy dtype: `LATENT_KEYFRAME`
    - Python dtype: `LatentKeyframeGroup`
- **`null_latent_kf_strength`**
    - The strength of the null latent keyframe, used to adjust the influence of latent keyframes within the interpolation.
    - Comfy dtype: `FLOAT`
    - Python dtype: `float`
- **`inherit_missing`**
    - A boolean flag indicating whether to inherit missing values from previous keyframes, ensuring continuity in the sequence.
    - Comfy dtype: `BOOLEAN`
    - Python dtype: `bool`
- **`mask_optional`**
    - An optional mask hint to be applied to the keyframes, providing additional control over the interpolation effect.
    - Comfy dtype: `MASK`
    - Python dtype: `Mask`
- **`print_keyframes`**
    - A boolean flag that, when enabled, prints the details of each keyframe to the log, aiding in debugging and visualization.
    - Comfy dtype: `BOOLEAN`
    - Python dtype: `bool`
## Output types
- **`TIMESTEP_KF`**
    - Comfy dtype: `TIMESTEP_KEYFRAME`
    - The resulting TimestepKeyframeGroup object containing the interpolated keyframes, ready for further processing or integration into the control network.
    - Python dtype: `TimestepKeyframeGroup`
## Usage tips
- Infra type: `CPU`
- Common nodes: unknown


## Source code
```python
class TimestepKeyframeInterpolationNode:
    @classmethod
    def INPUT_TYPES(s):
        return {
            "required": {
                "start_percent": ("FLOAT", {"default": 0.0, "min": 0.0, "max": 1.0, "step": 0.001},),
                "end_percent": ("FLOAT", {"default": 1.0, "min": 0.0, "max": 1.0, "step": 0.001}),
                "strength_start": ("FLOAT", {"default": 1.0, "min": 0.0, "max": 10.0, "step": 0.001},),
                "strength_end": ("FLOAT", {"default": 1.0, "min": 0.0, "max": 10.0, "step": 0.001},),
                "interpolation": (SI._LIST, ),
                "intervals": ("INT", {"default": 50, "min": 2, "max": 100, "step": 1}),
            },
            "optional": {
                "prev_timestep_kf": ("TIMESTEP_KEYFRAME", ),
                "cn_weights": ("CONTROL_NET_WEIGHTS", ),
                "latent_keyframe": ("LATENT_KEYFRAME", ),
                "null_latent_kf_strength": ("FLOAT", {"default": 0.0, "min": 0.0, "max": 10.0, "step": 0.001},),
                "inherit_missing": ("BOOLEAN", {"default": True},),
                "mask_optional": ("MASK", ),
                "print_keyframes": ("BOOLEAN", {"default": False}),
            }
        }
    
    RETURN_NAMES = ("TIMESTEP_KF", )
    RETURN_TYPES = ("TIMESTEP_KEYFRAME", )
    FUNCTION = "load_keyframe"

    CATEGORY = "Adv-ControlNet 🛂🅐🅒🅝/keyframes"

    def load_keyframe(self,
                      start_percent: float, end_percent: float,
                      strength_start: float, strength_end: float, interpolation: str, intervals: int,
                      cn_weights: ControlWeights=None,
                      latent_keyframe: LatentKeyframeGroup=None,
                      prev_timestep_kf: TimestepKeyframeGroup=None,
                      null_latent_kf_strength: float=0.0,
                      inherit_missing=True,
                      guarantee_steps=1,
                      mask_optional=None, print_keyframes=False):
        if not prev_timestep_kf:
            prev_timestep_kf = TimestepKeyframeGroup()
        else:
            prev_timestep_kf = prev_timestep_kf.clone()

        percents = SI.get_weights(num_from=start_percent, num_to=end_percent, length=intervals, method=SI.LINEAR)
        strengths = SI.get_weights(num_from=strength_start, num_to=strength_end, length=intervals, method=interpolation)

        is_first = True
        for percent, strength in zip(percents, strengths):
            guarantee_steps = 0
            if is_first:
                guarantee_steps = 1
                is_first = False
            prev_timestep_kf.add(TimestepKeyframe(start_percent=percent, strength=strength, null_latent_kf_strength=null_latent_kf_strength,
                                    control_weights=cn_weights, latent_keyframes=latent_keyframe, inherit_missing=inherit_missing,
                                    guarantee_steps=guarantee_steps, mask_hint_orig=mask_optional))
            if print_keyframes:
                logger.info(f"TimestepKeyframe - start_percent:{percent} = {strength}")
        return (prev_timestep_kf,)

```
