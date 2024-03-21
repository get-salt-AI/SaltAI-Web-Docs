# Apply AnimateDiff Model (Adv.) 🎭🅐🅓②
## Documentation
- Class name: `ADE_ApplyAnimateDiffModel`
- Category: `Animate Diff 🎭🅐🅓/② Gen2 nodes ②`
- Output node: `False`

This node applies the AnimateDiff model in an advanced manner, allowing for more complex and detailed animation effects. It utilizes a subset of inputs from the normal ApplyAnimateDiffModelNode, enabling customization of motion through various parameters.
## Input types
### Required
- **`motion_model`**
    - Specifies the motion model to be applied. This is the core component that defines how the animation effect is generated.
    - Python dtype: `MotionModelPatcher`
    - Comfy dtype: `MOTION_MODEL_ADE`
- **`start_percent`**
    - Specifies the starting percentage of the animation effect, allowing for control over when the animation begins within the timeline.
    - Python dtype: `float`
    - Comfy dtype: `FLOAT`
- **`end_percent`**
    - Specifies the ending percentage of the animation effect, providing control over when the animation ends within the timeline.
    - Python dtype: `float`
    - Comfy dtype: `FLOAT`
### Optional
- **`motion_lora`**
    - Optional parameter that allows for the adjustment of motion using LoRA (Low-Rank Adaptation) techniques.
    - Python dtype: `MotionLoraList`
    - Comfy dtype: `MOTION_LORA`
- **`scale_multival`**
    - Allows for scaling of the animation effect, providing control over the intensity of the motion.
    - Python dtype: `Optional[MultiVal]`
    - Comfy dtype: `MULTIVAL`
- **`effect_multival`**
    - Enables customization of the animation effect through multiple values, offering flexibility in the animation's appearance.
    - Python dtype: `Optional[MultiVal]`
    - Comfy dtype: `MULTIVAL`
- **`ad_keyframes`**
    - Defines keyframes for the animation, allowing for precise control over the motion at specific points in the timeline.
    - Python dtype: `Optional[ADKeyframeGroup]`
    - Comfy dtype: `AD_KEYFRAMES`
- **`prev_m_models`**
    - Optional parameter that includes previous motion models to be considered in the animation process, enabling more complex layering and sequencing of animations.
    - Python dtype: `Optional[MotionModelGroup]`
    - Comfy dtype: `M_MODELS`
## Output types
- **`m_models`**
    - Outputs the modified motion models after applying the AnimateDiff model, reflecting the changes made through the input parameters.
    - Python dtype: `MotionModelGroup`
    - Comfy dtype: `M_MODELS`
## Usage tips
- Infra type: `GPU`
- Common nodes: `ADE_UseEvolvedSampling`


## Source code
```python
class ApplyAnimateDiffModelNode:
    @classmethod
    def INPUT_TYPES(s):
        return {
            "required": {
                "motion_model": ("MOTION_MODEL_ADE",),
                "start_percent": ("FLOAT", {"default": 0.0, "min": 0.0, "max": 1.0, "step": 0.001}),
                "end_percent": ("FLOAT", {"default": 1.0, "min": 0.0, "max": 1.0, "step": 0.001}),
            },
            "optional": {
                "motion_lora": ("MOTION_LORA",),
                "scale_multival": ("MULTIVAL",),
                "effect_multival": ("MULTIVAL",),
                "ad_keyframes": ("AD_KEYFRAMES",),
                "prev_m_models": ("M_MODELS",),
            }
        }
    
    RETURN_TYPES = ("M_MODELS",)
    CATEGORY = "Animate Diff 🎭🅐🅓/② Gen2 nodes ②"
    FUNCTION = "apply_motion_model"

    def apply_motion_model(self, motion_model: MotionModelPatcher, start_percent: float=0.0, end_percent: float=1.0,
                           motion_lora: MotionLoraList=None, ad_keyframes: ADKeyframeGroup=None,
                           scale_multival=None, effect_multival=None,
                           prev_m_models: MotionModelGroup=None,):
        # set up motion models list
        if prev_m_models is None:
            prev_m_models = MotionModelGroup()
        prev_m_models = prev_m_models.clone()
        motion_model = motion_model.clone()
        # check if internal motion model already present in previous model - create new if so
        for prev_model in prev_m_models.models:
            if motion_model.model is prev_model.model:
                # need to create new internal model based on same state_dict
                motion_model = create_fresh_motion_module(motion_model)
        # apply motion model to loaded_mm
        if motion_lora is not None:
            for lora in motion_lora.loras:
                load_motion_lora_as_patches(motion_model, lora)
        motion_model.scale_multival = scale_multival
        motion_model.effect_multival = effect_multival
        motion_model.keyframes = ad_keyframes.clone() if ad_keyframes else ADKeyframeGroup()
        motion_model.timestep_percent_range = (start_percent, end_percent)
        # add to beginning, so that after injection, it will be the earliest of prev_m_models to be run
        prev_m_models.add_to_start(mm=motion_model)
        return (prev_m_models,)

```
