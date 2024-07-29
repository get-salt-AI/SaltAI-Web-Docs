---
tags:
- Counting
- Curve
- Interpolation
- WavePatterns
---

# Timestep Keyframe From List 🛂🅐🅒🅝
## Documentation
- Class name: `ACN_TimestepKeyframeFromStrengthList`
- Category: `Adv-ControlNet 🛂🅐🅒🅝/keyframes`
- Output node: `False`

This node is designed to generate a sequence of timestep keyframes based on a list of strengths, facilitating the creation of dynamic and customizable animation paths within the Advanced ControlNet framework. It allows for the precise control of animation intensity and timing through keyframe interpolation, supporting a variety of interpolation methods to achieve smooth transitions between keyframes.
## Input types
### Required
- **`float_strengths`**
    - A list of numerical values representing the strengths of each keyframe in the sequence, which dictate the intensity of the animation at various points.
    - Comfy dtype: `FLOAT`
    - Python dtype: `Union[float, list[float]]`
- **`start_percent`**
    - Specifies the starting percentage of the animation timeline where the keyframe sequence begins, enabling precise control over the timing of animations.
    - Comfy dtype: `FLOAT`
    - Python dtype: `float`
- **`end_percent`**
    - Defines the ending percentage of the animation timeline for the keyframe sequence, allowing for the delineation of the animation's duration within the overall timeline.
    - Comfy dtype: `FLOAT`
    - Python dtype: `float`
### Optional
- **`prev_timestep_kf`**
    - References a previous TimestepKeyframeGroup to which the new keyframes will be added, allowing for the continuation or modification of an existing animation sequence.
    - Comfy dtype: `TIMESTEP_KEYFRAME`
    - Python dtype: `TimestepKeyframeGroup`
- **`cn_weights`**
    - Control network weights that influence the animation's behavior, providing a mechanism for fine-tuning the animation's appearance.
    - Comfy dtype: `CONTROL_NET_WEIGHTS`
    - Python dtype: `ControlWeights`
- **`latent_keyframe`**
    - Specifies a group of latent keyframes that can be used to influence the animation, offering additional control over the animation's characteristics.
    - Comfy dtype: `LATENT_KEYFRAME`
    - Python dtype: `LatentKeyframeGroup`
- **`null_latent_kf_strength`**
    - Determines the strength of the null latent keyframe, affecting the baseline intensity of the animation.
    - Comfy dtype: `FLOAT`
    - Python dtype: `float`
- **`inherit_missing`**
    - A boolean flag that indicates whether missing keyframes should be inherited from the previous set, ensuring continuity in the animation.
    - Comfy dtype: `BOOLEAN`
    - Python dtype: `bool`
- **`mask_optional`**
    - An optional mask that can be applied to the keyframes, allowing for localized control over the animation.
    - Comfy dtype: `MASK`
    - Python dtype: `Mask`
- **`print_keyframes`**
    - A flag that, when enabled, prints the details of each keyframe to the console for debugging or informational purposes.
    - Comfy dtype: `BOOLEAN`
    - Python dtype: `bool`
## Output types
- **`TIMESTEP_KF`**
    - Comfy dtype: `TIMESTEP_KEYFRAME`
    - Returns an updated TimestepKeyframeGroup object that includes the newly generated keyframes, ready for further manipulation or rendering within the animation pipeline.
    - Python dtype: `TimestepKeyframeGroup`
## Usage tips
- Infra type: `CPU`
- Common nodes: unknown


## Source code
```python
class TimestepKeyframeFromStrengthListNode:
    @classmethod
    def INPUT_TYPES(s):
        return {
            "required": {
                "float_strengths": ("FLOAT", {"default": -1, "min": -1, "step": 0.001, "forceInput": True}),
                "start_percent": ("FLOAT", {"default": 0.0, "min": 0.0, "max": 1.0, "step": 0.001},),
                "end_percent": ("FLOAT", {"default": 1.0, "min": 0.0, "max": 1.0, "step": 0.001}),
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
                      float_strengths: float,
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

        if type(float_strengths) in (float, int):
            float_strengths = [float(float_strengths)]
        elif isinstance(float_strengths, Iterable):
            pass
        else:
            raise Exception(f"strengths_float must be either an iterable input or a float, but was {type(float_strengths).__repr__}.")
        percents = SI.get_weights(num_from=start_percent, num_to=end_percent, length=len(float_strengths), method=SI.LINEAR)

        is_first = True
        for percent, strength in zip(percents, float_strengths):
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
