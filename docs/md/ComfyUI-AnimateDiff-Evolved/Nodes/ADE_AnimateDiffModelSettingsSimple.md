# [DEPR] Motion Model Settings (Simple) 🎭🅐🅓①
## Documentation
- Class name: `ADE_AnimateDiffModelSettingsSimple`
- Category: ``
- Output node: `False`

This node is designed to configure the motion model settings for AnimateDiff in a simplified manner. It allows users to adjust the scale of motion in animations by specifying minimum and maximum motion scales, optionally including a mask to control motion scale at a more granular level.
## Input types
### Required
- **`motion_pe_stretch`**
    - Specifies the amount by which the positional encoding (PE) is stretched, affecting the motion's perceived speed and intensity in the animation.
    - Python dtype: `int`
    - Comfy dtype: `INT`
### Optional
- **`mask_motion_scale`**
    - An optional mask that allows for more detailed control over the motion scale across different parts of the animation, enabling variable motion effects.
    - Python dtype: `torch.Tensor`
    - Comfy dtype: `MASK`
- **`min_motion_scale`**
    - Defines the minimum scale for motion in the animation, setting a lower bound on how subtle the motion effects can be.
    - Python dtype: `float`
    - Comfy dtype: `FLOAT`
- **`max_motion_scale`**
    - Sets the maximum scale for motion, determining the upper limit of how pronounced the motion effects can appear in the animation.
    - Python dtype: `float`
    - Comfy dtype: `FLOAT`
## Output types
- **`ad_settings`**
    - Outputs the configured motion model settings for use in AnimateDiff animations.
    - Python dtype: `AnimateDiffSettings`
    - Comfy dtype: `AD_SETTINGS`
## Usage tips
- Infra type: `GPU`
- Common nodes: unknown


## Source code
```python
class AnimateDiffModelSettingsSimple:
    @classmethod
    def INPUT_TYPES(s):
        return {
            "required": {
                "motion_pe_stretch": ("INT", {"default": 0, "min": 0, "step": 1}),
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

    def get_motion_model_settings(self, motion_pe_stretch: int,
                                  mask_motion_scale: torch.Tensor=None, min_motion_scale: float=1.0, max_motion_scale: float=1.0):
        adjust_pe = AdjustPEGroup(AdjustPE(motion_pe_stretch=motion_pe_stretch))
        motion_model_settings = AnimateDiffSettings(
            adjust_pe=adjust_pe,
            mask_attn_scale=mask_motion_scale,
            mask_attn_scale_min=min_motion_scale,
            mask_attn_scale_max=max_motion_scale,
            )

        return (motion_model_settings,)

```
