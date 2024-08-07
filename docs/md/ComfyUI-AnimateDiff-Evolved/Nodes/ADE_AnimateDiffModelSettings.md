---
tags:
- AnimateDiff
- Animation
---

# 🚫[DEPR] Motion Model Settings (Advanced) 🎭🅐🅓①
## Documentation
- Class name: `ADE_AnimateDiffModelSettings`
- Category: ``
- Output node: `False`

This node is designed to configure motion model settings for the AnimateDiff process, allowing users to adjust motion scale parameters and apply optional mask motion scaling to fine-tune the animation effects.
## Input types
### Required
- **`pe_strength`**
    - Determines the strength of positional encoding adjustments, influencing the overall animation dynamics.
    - Comfy dtype: `FLOAT`
    - Python dtype: `float`
- **`attn_strength`**
    - Controls the strength of attention adjustments, affecting how motion influences are applied across the animation.
    - Comfy dtype: `FLOAT`
    - Python dtype: `float`
- **`other_strength`**
    - Adjusts the strength of other model parameters not explicitly categorized, offering additional fine-tuning of the animation.
    - Comfy dtype: `FLOAT`
    - Python dtype: `float`
- **`motion_pe_stretch`**
    - Specifies the amount by which positional encoding is stretched, altering the motion's perceived speed and extent.
    - Comfy dtype: `INT`
    - Python dtype: `int`
- **`cap_initial_pe_length`**
    - Caps the initial positional encoding length, potentially limiting the range of motion.
    - Comfy dtype: `INT`
    - Python dtype: `int`
- **`interpolate_pe_to_length`**
    - Defines the length to which positional encoding is interpolated, affecting the smoothness and range of motion.
    - Comfy dtype: `INT`
    - Python dtype: `int`
- **`initial_pe_idx_offset`**
    - Sets the initial positional encoding index offset, adjusting the starting point of the motion.
    - Comfy dtype: `INT`
    - Python dtype: `int`
- **`final_pe_idx_offset`**
    - Determines the final positional encoding index offset, adjusting the endpoint of the motion.
    - Comfy dtype: `INT`
    - Python dtype: `int`
### Optional
- **`mask_motion_scale`**
    - An optional mask to scale motion selectively across different parts of the image, enabling more detailed control over the animation.
    - Comfy dtype: `MASK`
    - Python dtype: `torch.Tensor`
- **`min_motion_scale`**
    - Specifies the minimum scale for motion, influencing the subtlety of the animation. Lower values result in more subtle motion effects.
    - Comfy dtype: `FLOAT`
    - Python dtype: `float`
- **`max_motion_scale`**
    - Defines the maximum scale for motion, affecting the intensity of the animation. Higher values lead to more pronounced motion effects.
    - Comfy dtype: `FLOAT`
    - Python dtype: `float`
## Output types
- **`ad_settings`**
    - Comfy dtype: `AD_SETTINGS`
    - Outputs the configured motion model settings, encapsulating adjustments to motion scale and optional mask motion scaling.
    - Python dtype: `AnimateDiffSettings`
## Usage tips
- Infra type: `CPU`
- Common nodes: unknown


## Source code
```python
class AnimateDiffModelSettingsAdvanced:
    @classmethod
    def INPUT_TYPES(s):
        return {
            "required": {
                "pe_strength": ("FLOAT", {"default": 1.0, "min": 0.0, "max": 10.0, "step": 0.0001}),
                "attn_strength": ("FLOAT", {"default": 1.0, "min": 0.0, "max": 10.0, "step": 0.0001}),
                "other_strength": ("FLOAT", {"default": 1.0, "min": 0.0, "max": 10.0, "step": 0.0001}),
                "motion_pe_stretch": ("INT", {"default": 0, "min": 0, "step": 1}),
                "cap_initial_pe_length": ("INT", {"default": 0, "min": 0, "step": 1}),
                "interpolate_pe_to_length": ("INT", {"default": 0, "min": 0, "step": 1}),
                "initial_pe_idx_offset": ("INT", {"default": 0, "min": 0, "step": 1}),
                "final_pe_idx_offset": ("INT", {"default": 0, "min": 0, "step": 1}),
            },
            "optional": {
                "mask_motion_scale": ("MASK",),
                "min_motion_scale": ("FLOAT", {"default": 1.0, "min": 0.0, "step": 0.001}),
                "max_motion_scale": ("FLOAT", {"default": 1.0, "min": 0.0, "step": 0.001}),
                "optional": {"deprecation_warning": ("ADEWARN", {"text": "Deprecated"})},
            }
        }
    
    RETURN_TYPES = ("AD_SETTINGS",)
    CATEGORY = ""  #"Animate Diff 🎭🅐🅓/① Gen1 nodes ①/motion settings/experimental"
    FUNCTION = "get_motion_model_settings"

    def get_motion_model_settings(self, pe_strength: float, attn_strength: float, other_strength: float,
                                  motion_pe_stretch: int,
                                  cap_initial_pe_length: int, interpolate_pe_to_length: int,
                                  initial_pe_idx_offset: int, final_pe_idx_offset: int,
                                  mask_motion_scale: torch.Tensor=None, min_motion_scale: float=1.0, max_motion_scale: float=1.0):
        adjust_pe = AdjustGroup(AdjustPE(motion_pe_stretch=motion_pe_stretch,
                             cap_initial_pe_length=cap_initial_pe_length, interpolate_pe_to_length=interpolate_pe_to_length,
                             initial_pe_idx_offset=initial_pe_idx_offset, final_pe_idx_offset=final_pe_idx_offset))
        adjust_weight = AdjustGroup(AdjustWeight(
            pe_MULT=pe_strength,
            attn_MULT=attn_strength,
            other_MULT=other_strength,
        ))
        motion_model_settings = AnimateDiffSettings(
            adjust_pe=adjust_pe,
            adjust_weight=adjust_weight,
            mask_attn_scale=mask_motion_scale,
            mask_attn_scale_min=min_motion_scale,
            mask_attn_scale_max=max_motion_scale,
        )

        return (motion_model_settings,)

```
