# [DEPR] Motion Model Settings 🎭🅐🅓①
## Documentation
- Class name: `ADE_AnimateDiffModelSettings_Release`
- Category: ``
- Output node: `False`

This node is responsible for configuring the motion model settings for the AnimateDiff process. It allows for the adjustment of parameters related to the positional encoding (PE) stretch, attention strengths, and motion scale, which are crucial for achieving desired animation effects.
## Input types
### Required
- **`min_motion_scale`**
    - Sets the minimum scale for motion, ensuring that motion effects do not fall below a certain threshold.
    - Python dtype: `float`
    - Comfy dtype: `FLOAT`
- **`max_motion_scale`**
    - Defines the maximum scale for motion, capping the intensity of motion effects.
    - Python dtype: `float`
    - Comfy dtype: `FLOAT`
### Optional
- **`mask_motion_scale`**
    - Provides a tensor to scale the motion mask, which can modify how motion is applied across different parts of the image.
    - Python dtype: `torch.Tensor`
    - Comfy dtype: `TENSOR`
## Output types
- **`ad_settings`**
    - The configured motion model settings, encapsulating all adjustments made for the AnimateDiff animation process.
    - Python dtype: `AnimateDiffSettings`
    - Comfy dtype: `AD_SETTINGS`
## Usage tips
- Infra type: `GPU`
- Common nodes: unknown


## Source code
```python
class AnimateDiffModelSettings:
    @classmethod
    def INPUT_TYPES(s):
        return {
            "required": {
                "min_motion_scale": ("FLOAT", {"default": 1.0, "min": 0.0, "step": 0.001}),
                "max_motion_scale": ("FLOAT", {"default": 1.0, "min": 0.0, "step": 0.001}),
            },
            "optional": {
                "mask_motion_scale": ("MASK",),
            }
        }
    
    RETURN_TYPES = ("AD_SETTINGS",)
    CATEGORY = ""  #"Animate Diff 🎭🅐🅓/① Gen1 nodes ①/motion settings"
    FUNCTION = "get_motion_model_settings"

    def get_motion_model_settings(self, mask_motion_scale: torch.Tensor=None, min_motion_scale: float=1.0, max_motion_scale: float=1.0):
        motion_model_settings = AnimateDiffSettings(
            mask_attn_scale=mask_motion_scale,
            mask_attn_scale_min=min_motion_scale,
            mask_attn_scale_max=max_motion_scale,
            )

        return (motion_model_settings,)

```
