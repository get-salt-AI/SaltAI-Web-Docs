# [DEPR] Motion Model Settings (Advanced) 🎭🅐🅓①
## Documentation
- Class name: `ADE_AnimateDiffModelSettings`
- Category: ``
- Output node: `False`

This node is designed for configuring advanced motion model settings within the AnimateDiff framework. It allows for detailed customization of the motion model's behavior, including adjustments to positional encoding (PE) stretch, attention strengths, and motion scale, to achieve desired animation effects.
## Input types
### Required
- **`pe_strength`**
    - Specifies the strength of the positional encoding adjustments. This parameter influences the intensity of the motion effect applied to the generated animation.
    - Python dtype: `float`
    - Comfy dtype: `FLOAT`
- **`attn_strength`**
    - Determines the strength of the attention mechanism adjustments. It affects how the model focuses on different parts of the image during the animation process.
    - Python dtype: `float`
    - Comfy dtype: `FLOAT`
- **`other_strength`**
    - Controls other unspecified model adjustments that contribute to the overall animation effect.
    - Python dtype: `float`
    - Comfy dtype: `FLOAT`
- **`motion_pe_stretch`**
    - Defines the extent to which positional encoding is stretched, impacting the motion's perceived speed and fluidity.
    - Python dtype: `int`
    - Comfy dtype: `INT`
- **`cap_initial_pe_length`**
    - Sets a cap on the initial length of positional encoding, which can affect the starting point of the animation.
    - Python dtype: `int`
    - Comfy dtype: `INT`
- **`interpolate_pe_to_length`**
    - Specifies the target length for positional encoding interpolation, influencing the animation's progression.
    - Python dtype: `int`
    - Comfy dtype: `INT`
- **`initial_pe_idx_offset`**
    - Adjusts the initial index offset for positional encoding, affecting the starting frame of the animation.
    - Python dtype: `int`
    - Comfy dtype: `INT`
- **`final_pe_idx_offset`**
    - Modifies the final index offset for positional encoding, determining the ending frame of the animation.
    - Python dtype: `int`
    - Comfy dtype: `INT`
### Optional
- **`mask_motion_scale`**
    - Applies a scaling factor to the motion mask, allowing for fine-tuning of the motion's intensity and direction.
    - Python dtype: `torch.Tensor`
    - Comfy dtype: `MASK`
- **`min_motion_scale`**
    - Sets the minimum scaling factor for motion, ensuring that the animation's intensity does not fall below a certain threshold.
    - Python dtype: `float`
    - Comfy dtype: `FLOAT`
- **`max_motion_scale`**
    - Defines the maximum scaling factor for motion, capping the animation's intensity to prevent overly dramatic effects.
    - Python dtype: `float`
    - Comfy dtype: `FLOAT`
## Output types
- **`ad_settings`**
    - The configured motion model settings, encapsulating all adjustments made to the model's behavior for animation purposes.
    - Python dtype: `AnimateDiffSettings`
    - Comfy dtype: `AD_SETTINGS`
## Usage tips
- Infra type: `GPU`
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
        adjust_pe = AdjustPEGroup(AdjustPE(motion_pe_stretch=motion_pe_stretch,
                             cap_initial_pe_length=cap_initial_pe_length, interpolate_pe_to_length=interpolate_pe_to_length,
                             initial_pe_idx_offset=initial_pe_idx_offset, final_pe_idx_offset=final_pe_idx_offset))
        motion_model_settings = AnimateDiffSettings(
            adjust_pe=adjust_pe,
            pe_strength=pe_strength,
            attn_strength=attn_strength,
            other_strength=other_strength,
            mask_attn_scale=mask_motion_scale,
            mask_attn_scale_min=min_motion_scale,
            mask_attn_scale_max=max_motion_scale,
            )

        return (motion_model_settings,)

```
