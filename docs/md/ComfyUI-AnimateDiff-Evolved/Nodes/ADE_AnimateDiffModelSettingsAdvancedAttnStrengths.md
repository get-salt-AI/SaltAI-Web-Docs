# [DEPR] Motion Model Settings (Adv. Attn) 🎭🅐🅓①
## Documentation
- Class name: `ADE_AnimateDiffModelSettingsAdvancedAttnStrengths`
- Category: ``
- Output node: `False`

This node is designed to configure advanced attention strengths for the AnimateDiff model. It allows for fine-tuning of the model's attention mechanism by adjusting specific strengths related to query, key, value, and attention output weights and biases. This customization can lead to more precise control over the animation and diffusion process, potentially enhancing the quality of generated animations.
## Input types
### Required
- **`pe_strength`**
    - Defines the strength of positional encoding adjustments. It influences how much the positional encodings are scaled, affecting the spatial coherence of the generated animation.
    - Python dtype: `float`
    - Comfy dtype: `FLOAT`
- **`attn_strength`**
    - Sets the overall strength of the attention mechanism. This parameter scales the attention weights, impacting how the model focuses on different parts of the input.
    - Python dtype: `float`
    - Comfy dtype: `FLOAT`
- **`attn_q_strength`**
    - unknown
    - Python dtype: `unknown`
    - Comfy dtype: `FLOAT`
- **`attn_k_strength`**
    - unknown
    - Python dtype: `unknown`
    - Comfy dtype: `FLOAT`
- **`attn_v_strength`**
    - unknown
    - Python dtype: `unknown`
    - Comfy dtype: `FLOAT`
- **`attn_out_weight_strength`**
    - unknown
    - Python dtype: `unknown`
    - Comfy dtype: `FLOAT`
- **`attn_out_bias_strength`**
    - unknown
    - Python dtype: `unknown`
    - Comfy dtype: `FLOAT`
- **`other_strength`**
    - Adjusts the strength of other model components outside the attention mechanism. This can include various model parameters not directly related to attention, providing a way to fine-tune the model's behavior.
    - Python dtype: `float`
    - Comfy dtype: `FLOAT`
- **`motion_pe_stretch`**
    - Specifies the stretch factor for motion positional encoding. It determines how much the positional encodings related to motion are stretched, affecting the perceived motion in the animation.
    - Python dtype: `int`
    - Comfy dtype: `INT`
- **`cap_initial_pe_length`**
    - Caps the initial length of positional encodings. This limit can help in controlling the range of positional encoding adjustments, ensuring they stay within a desired scope.
    - Python dtype: `int`
    - Comfy dtype: `INT`
- **`interpolate_pe_to_length`**
    - Defines the target length for interpolating positional encodings. This parameter controls the interpolation of positional encodings to a specific length, affecting the granularity of spatial adjustments.
    - Python dtype: `int`
    - Comfy dtype: `INT`
- **`initial_pe_idx_offset`**
    - Sets the initial offset for positional encoding indices. This offset shifts the starting point of positional encodings, potentially altering the animation's starting spatial context.
    - Python dtype: `int`
    - Comfy dtype: `INT`
- **`final_pe_idx_offset`**
    - Determines the final offset for positional encoding indices. Similar to the initial offset, this parameter shifts the ending point of positional encodings, affecting the animation's ending spatial context.
    - Python dtype: `int`
    - Comfy dtype: `INT`
### Optional
- **`mask_motion_scale`**
    - Applies a mask to scale motion-related adjustments. This tensor can selectively scale motion adjustments across different parts of the input, allowing for more nuanced control over the animation.
    - Python dtype: `torch.Tensor`
    - Comfy dtype: `MASK`
- **`min_motion_scale`**
    - Sets the minimum scale for motion adjustments. This parameter ensures that motion-related scaling does not fall below a certain threshold, maintaining a base level of motion intensity.
    - Python dtype: `float`
    - Comfy dtype: `FLOAT`
- **`max_motion_scale`**
    - Defines the maximum scale for motion adjustments. It caps the intensity of motion-related scaling, preventing overly drastic motion effects.
    - Python dtype: `float`
    - Comfy dtype: `FLOAT`
## Output types
- **`ad_settings`**
    - The configured settings for the AnimateDiff model, including advanced attention strengths and other adjustments. These settings are used to customize the model's behavior for generating animations.
    - Python dtype: `AnimateDiffSettings`
    - Comfy dtype: `AD_SETTINGS`
## Usage tips
- Infra type: `GPU`
- Common nodes: unknown


## Source code
```python
class AnimateDiffModelSettingsAdvancedAttnStrengths:
    @classmethod
    def INPUT_TYPES(s):
        return {
            "required": {
                "pe_strength": ("FLOAT", {"default": 1.0, "min": 0.0, "max": 10.0, "step": 0.0001}),
                "attn_strength": ("FLOAT", {"default": 1.0, "min": 0.0, "max": 10.0, "step": 0.0001}),
                "attn_q_strength": ("FLOAT", {"default": 1.0, "min": 0.0, "max": 10.0, "step": 0.0001}),
                "attn_k_strength": ("FLOAT", {"default": 1.0, "min": 0.0, "max": 10.0, "step": 0.0001}),
                "attn_v_strength": ("FLOAT", {"default": 1.0, "min": 0.0, "max": 10.0, "step": 0.0001}),
                "attn_out_weight_strength": ("FLOAT", {"default": 1.0, "min": 0.0, "max": 10.0, "step": 0.0001}),
                "attn_out_bias_strength": ("FLOAT", {"default": 1.0, "min": 0.0, "max": 10.0, "step": 0.0001}),
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

    def get_motion_model_settings(self, pe_strength: float, attn_strength: float,
                                  attn_q_strength: float,
                                  attn_k_strength: float,
                                  attn_v_strength: float,
                                  attn_out_weight_strength: float,
                                  attn_out_bias_strength: float,
                                  other_strength: float,
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
            attn_q_strength=attn_q_strength,
            attn_k_strength=attn_k_strength,
            attn_v_strength=attn_v_strength,
            attn_out_weight_strength=attn_out_weight_strength,
            attn_out_bias_strength=attn_out_bias_strength,
            other_strength=other_strength,
            mask_attn_scale=mask_motion_scale,
            mask_attn_scale_min=min_motion_scale,
            mask_attn_scale_max=max_motion_scale,
            )

        return (motion_model_settings,)

```
