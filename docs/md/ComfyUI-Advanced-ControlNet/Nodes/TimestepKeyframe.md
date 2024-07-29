---
tags:
- Counting
- Curve
- Interpolation
- WavePatterns
---

# Timestep Keyframe 🛂🅐🅒🅝
## Documentation
- Class name: `TimestepKeyframe`
- Category: `Adv-ControlNet 🛂🅐🅒🅝/keyframes`
- Output node: `False`

The TimestepKeyframe node represents a specific point within a sequence of operations, characterized by its start percentage, strength, and various optional parameters such as control weights, latent keyframes, and mask hints. It serves as a foundational element in defining the behavior and influence of keyframes over the course of an animation or transformation process, allowing for precise control over the interpolation and application of effects at specific intervals.
## Input types
### Required
- **`start_percent`**
    - Specifies the starting percentage of the keyframe within the sequence, determining its position relative to the entire animation or transformation process. It is crucial for defining the timing of when the keyframe's effects should begin to take effect.
    - Comfy dtype: `FLOAT`
    - Python dtype: `float`
### Optional
- **`prev_timestep_kf`**
    - Optional parameter that specifies the previous timestep keyframe group, allowing for the continuation or branching of keyframe sequences.
    - Comfy dtype: `TIMESTEP_KEYFRAME`
    - Python dtype: `TimestepKeyframeGroup`
- **`strength`**
    - Defines the intensity or magnitude of the keyframe's effect, influencing how strongly it impacts the animation or transformation at the specified interval.
    - Comfy dtype: `FLOAT`
    - Python dtype: `float`
- **`cn_weights`**
    - Optional parameter that specifies the weights for controlling the influence of this keyframe on different aspects of the animation or transformation, allowing for fine-tuned adjustments.
    - Comfy dtype: `CONTROL_NET_WEIGHTS`
    - Python dtype: `ControlWeights`
- **`latent_keyframe`**
    - unknown
    - Comfy dtype: `LATENT_KEYFRAME`
    - Python dtype: `unknown`
- **`null_latent_kf_strength`**
    - Specifies the strength of the null latent keyframe, offering a way to adjust the baseline influence of latent keyframes within the sequence.
    - Comfy dtype: `FLOAT`
    - Python dtype: `float`
- **`inherit_missing`**
    - A boolean flag indicating whether missing attributes from previous keyframes should be inherited, ensuring continuity and consistency across the sequence.
    - Comfy dtype: `BOOLEAN`
    - Python dtype: `bool`
- **`guarantee_steps`**
    - Determines the minimum number of steps for which the keyframe's effects are guaranteed to be applied, ensuring a certain level of persistence in its influence.
    - Comfy dtype: `INT`
    - Python dtype: `int`
- **`mask_optional`**
    - Optional parameter that provides a hint for masking, enabling selective application of the keyframe's effects based on spatial considerations.
    - Comfy dtype: `MASK`
    - Python dtype: `Tensor`
## Output types
- **`TIMESTEP_KF`**
    - Comfy dtype: `TIMESTEP_KEYFRAME`
    - Represents the updated or newly created timestep keyframe group after processing the input parameters, encapsulating the sequence of keyframes with their specified attributes and effects.
    - Python dtype: `TimestepKeyframeGroup`
## Usage tips
- Infra type: `CPU`
- Common nodes: unknown


## Source code
```python
class TimestepKeyframeNode:
    OUTDATED_DUMMY = -39

    @classmethod
    def INPUT_TYPES(s):
        return {
            "required": {
                "start_percent": ("FLOAT", {"default": 0.0, "min": 0.0, "max": 1.0, "step": 0.001}, ),
            },
            "optional": {
                "prev_timestep_kf": ("TIMESTEP_KEYFRAME", ),
                "strength": ("FLOAT", {"default": 1.0, "min": 0.0, "max": 10.0, "step": 0.001}, ),
                "cn_weights": ("CONTROL_NET_WEIGHTS", ),
                "latent_keyframe": ("LATENT_KEYFRAME", ),
                "null_latent_kf_strength": ("FLOAT", {"default": 0.0, "min": 0.0, "max": 10.0, "step": 0.001}, ),
                "inherit_missing": ("BOOLEAN", {"default": True}, ),
                "guarantee_steps": ("INT", {"default": 1, "min": 0, "max": BIGMAX}),
                "mask_optional": ("MASK", ),
            }
        }
    
    RETURN_NAMES = ("TIMESTEP_KF", )
    RETURN_TYPES = ("TIMESTEP_KEYFRAME", )
    FUNCTION = "load_keyframe"

    CATEGORY = "Adv-ControlNet 🛂🅐🅒🅝/keyframes"

    def load_keyframe(self,
                      start_percent: float,
                      strength: float=1.0,
                      cn_weights: ControlWeights=None, control_net_weights: ControlWeights=None, # old name
                      latent_keyframe: LatentKeyframeGroup=None,
                      prev_timestep_kf: TimestepKeyframeGroup=None, prev_timestep_keyframe: TimestepKeyframeGroup=None, # old name
                      null_latent_kf_strength: float=0.0,
                      inherit_missing=True,
                      guarantee_steps=OUTDATED_DUMMY,
                      guarantee_usage=True, # old input
                      mask_optional=None,):
        # if using outdated dummy value, means node on workflow is outdated and should appropriately convert behavior
        if guarantee_steps == self.OUTDATED_DUMMY:
            guarantee_steps = int(guarantee_usage)
        control_net_weights = control_net_weights if control_net_weights else cn_weights
        prev_timestep_keyframe = prev_timestep_keyframe if prev_timestep_keyframe else prev_timestep_kf
        if not prev_timestep_keyframe:
            prev_timestep_keyframe = TimestepKeyframeGroup()
        else:
            prev_timestep_keyframe = prev_timestep_keyframe.clone()
        keyframe = TimestepKeyframe(start_percent=start_percent, strength=strength, null_latent_kf_strength=null_latent_kf_strength,
                                    control_weights=control_net_weights, latent_keyframes=latent_keyframe, inherit_missing=inherit_missing,
                                    guarantee_steps=guarantee_steps, mask_hint_orig=mask_optional)
        prev_timestep_keyframe.add(keyframe)
        return (prev_timestep_keyframe,)

```
