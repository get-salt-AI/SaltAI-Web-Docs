# Apply AnimateDiff Model 🎭🅐🅓②
## Documentation
- Class name: `ADE_ApplyAnimateDiffModelSimple`
- Category: `Animate Diff 🎭🅐🅓/② Gen2 nodes ②`
- Output node: `False`

This node applies a simplified version of the AnimateDiff model to generate animations. It uses a subset of the normal ApplyAnimateDiffModelNode inputs, focusing on essential parameters for motion application.
## Input types
### Required
- **`motion_model`**
    - Specifies the motion model to be used for animation. It's a critical component that defines how the animation behaves.
    - Python dtype: `MotionModelPatcher`
    - Comfy dtype: `MOTION_MODEL_ADE`
### Optional
- **`motion_lora`**
    - Optional parameter that allows for the adjustment of motion using LoRA (Low-Rank Adaptation) techniques.
    - Python dtype: `MotionLoraList`
    - Comfy dtype: `MOTION_LORA`
- **`scale_multival`**
    - Optional parameter for scaling the animation effect.
    - Python dtype: `float`
    - Comfy dtype: `MULTIVAL`
- **`effect_multival`**
    - Optional parameter for adjusting the intensity of the animation effect.
    - Python dtype: `float`
    - Comfy dtype: `MULTIVAL`
- **`ad_keyframes`**
    - Optional parameter for defining keyframes in the animation.
    - Python dtype: `ADKeyframes`
    - Comfy dtype: `AD_KEYFRAMES`
## Output types
- **`m_models`**
    - The result of applying the motion model to generate animations.
    - Python dtype: `MotionModelOutput`
    - Comfy dtype: `M_MODELS`
## Usage tips
- Infra type: `GPU`
- Common nodes: unknown


## Source code
```python
class ApplyAnimateDiffModelBasicNode:
    @classmethod
    def INPUT_TYPES(s):
        return {
            "required": {
                "motion_model": ("MOTION_MODEL_ADE",),
            },
            "optional": {
                "motion_lora": ("MOTION_LORA",),
                "scale_multival": ("MULTIVAL",),
                "effect_multival": ("MULTIVAL",),
                "ad_keyframes": ("AD_KEYFRAMES",),
            }
        }
    
    RETURN_TYPES = ("M_MODELS",)
    CATEGORY = "Animate Diff 🎭🅐🅓/② Gen2 nodes ②"
    FUNCTION = "apply_motion_model"

    def apply_motion_model(self,
                           motion_model: MotionModelPatcher, motion_lora: MotionLoraList=None,
                           scale_multival=None, effect_multival=None, ad_keyframes=None):
        # just a subset of normal ApplyAnimateDiffModelNode inputs
        return ApplyAnimateDiffModelNode.apply_motion_model(self, motion_model, motion_lora=motion_lora,
                                                            scale_multival=scale_multival, effect_multival=effect_multival,
                                                            ad_keyframes=ad_keyframes)

```
