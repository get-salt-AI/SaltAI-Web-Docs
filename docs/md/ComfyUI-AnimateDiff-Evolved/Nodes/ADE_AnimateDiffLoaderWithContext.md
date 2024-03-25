# AnimateDiff Loader [Legacy] 🎭🅐🅓①
## Documentation
- Class name: `ADE_AnimateDiffLoaderWithContext`
- Category: `Animate Diff 🎭🅐🅓/① Gen1 nodes ①`
- Output node: `False`

This node is designed for loading AnimateDiff models in a legacy context, facilitating the integration of animation dynamics into generative models. It aims to provide backward compatibility and ease the transition for models developed in earlier versions of the AnimateDiff framework.
## Input types
### Required
- **`model`**
    - Comfy dtype: `MODEL`
    - Specifies the generative model to be loaded and configured with AnimateDiff parameters, serving as the foundation for animation dynamics integration.
    - Python dtype: `ModelPatcher`
- **`model_name`**
    - Comfy dtype: `COMBO[STRING]`
    - Identifies the specific AnimateDiff model to be loaded, acting as a key identifier for selecting the appropriate animation dynamics.
    - Python dtype: `str`
- **`beta_schedule`**
    - Comfy dtype: `COMBO[STRING]`
    - Determines the beta schedule to be used, allowing for fine-tuned control over the diffusion process within the AnimateDiff framework.
    - Python dtype: `BetaSchedules`
### Optional
- **`context_options`**
    - Comfy dtype: `CONTEXT_OPTIONS`
    - Provides additional context-specific options for the AnimateDiff model, allowing for customized animation dynamics.
    - Python dtype: `ContextOptions`
- **`motion_lora`**
    - Comfy dtype: `MOTION_LORA`
    - Specifies the LoRA parameters for motion, enabling fine-tuned control over the animation dynamics.
    - Python dtype: `MotionLoraList`
- **`ad_settings`**
    - Comfy dtype: `AD_SETTINGS`
    - Defines the AnimateDiff settings to be applied, offering further customization of the animation dynamics.
    - Python dtype: `AnimateDiffSettings`
- **`sample_settings`**
    - Comfy dtype: `SAMPLE_SETTINGS`
    - Determines the sampling settings for the AnimateDiff model, affecting the quality and characteristics of the generated animation.
    - Python dtype: `SampleSettings`
- **`motion_scale`**
    - Comfy dtype: `FLOAT`
    - Adjusts the scale of motion in the animation, allowing for more subtle or exaggerated movements.
    - Python dtype: `float`
- **`apply_v2_models_properly`**
    - Comfy dtype: `BOOLEAN`
    - Ensures that version 2 models are applied correctly, maintaining compatibility and performance.
    - Python dtype: `bool`
- **`ad_keyframes`**
    - Comfy dtype: `AD_KEYFRAMES`
    - Specifies keyframes for the animation, enabling precise control over the animation timeline.
    - Python dtype: `ADKeyframesList`
## Output types
- **`model`**
    - Comfy dtype: `MODEL`
    - Returns the configured generative model, now enhanced with AnimateDiff animation dynamics for subsequent processing.
    - Python dtype: `ModelPatcher`
## Usage tips
- Infra type: `CPU`
- Common nodes: `KSampler,FreeU_V2,KSamplerAdvanced,LoraLoaderModelOnly,LoraLoader,ToBasicPipe,IPAdapterApply,DynamicThresholdingSimple,Reroute`


## Source code
```python
class LegacyAnimateDiffLoaderWithContext:
    @classmethod
    def INPUT_TYPES(s):
        return {
            "required": {
                "model": ("MODEL",),
                "model_name": (get_available_motion_models(),),
                "beta_schedule": (BetaSchedules.ALIAS_LIST, {"default": BetaSchedules.AUTOSELECT}),
                #"apply_mm_groupnorm_hack": ("BOOLEAN", {"default": True}),
            },
            "optional": {
                "context_options": ("CONTEXT_OPTIONS",),
                "motion_lora": ("MOTION_LORA",),
                "ad_settings": ("AD_SETTINGS",),
                "sample_settings": ("SAMPLE_SETTINGS",),
                "motion_scale": ("FLOAT", {"default": 1.0, "min": 0.0, "step": 0.001}),
                "apply_v2_models_properly": ("BOOLEAN", {"default": True}),
                "ad_keyframes": ("AD_KEYFRAMES",),
            }
        }
    
    RETURN_TYPES = ("MODEL",)
    CATEGORY = "Animate Diff 🎭🅐🅓/① Gen1 nodes ①"
    FUNCTION = "load_mm_and_inject_params"


    def load_mm_and_inject_params(self,
        model: ModelPatcher,
        model_name: str, beta_schedule: str,# apply_mm_groupnorm_hack: bool,
        context_options: ContextOptionsGroup=None, motion_lora: MotionLoraList=None, ad_settings: AnimateDiffSettings=None, motion_model_settings: AnimateDiffSettings=None,
        sample_settings: SampleSettings=None, motion_scale: float=1.0, apply_v2_models_properly: bool=False, ad_keyframes: ADKeyframeGroup=None,
    ):
        if ad_settings is not None:
            motion_model_settings = ad_settings
        # load motion module
        motion_model = load_motion_module_gen1(model_name, model, motion_lora=motion_lora, motion_model_settings=motion_model_settings)
        # set injection params
        params = InjectionParams(
                unlimited_area_hack=False,
                model_name=model_name,
                apply_v2_properly=apply_v2_models_properly,
        )
        if context_options:
            params.set_context(context_options)
        # set motion_scale and motion_model_settings
        if not motion_model_settings:
            motion_model_settings = AnimateDiffSettings()
        motion_model_settings.attn_scale = motion_scale
        params.set_motion_model_settings(motion_model_settings)

        if params.motion_model_settings.mask_attn_scale is not None:
            motion_model.scale_multival = params.motion_model_settings.mask_attn_scale * params.motion_model_settings.attn_scale
        else:
            motion_model.scale_multival = params.motion_model_settings.attn_scale

        motion_model.keyframes = ad_keyframes.clone() if ad_keyframes else ADKeyframeGroup()

        model = ModelPatcherAndInjector(model)
        model.motion_models = MotionModelGroup(motion_model)
        model.sample_settings = sample_settings if sample_settings is not None else SampleSettings()
        model.motion_injection_params = params

        # save model sampling from BetaSchedule as object patch
        # if autoselect, get suggested beta_schedule from motion model
        if beta_schedule == BetaSchedules.AUTOSELECT and not model.motion_models.is_empty():
            beta_schedule = model.motion_models[0].model.get_best_beta_schedule(log=True)
        new_model_sampling = BetaSchedules.to_model_sampling(beta_schedule, model)
        if new_model_sampling is not None:
            model.add_object_patch("model_sampling", new_model_sampling)

        del motion_model
        return (model,)

```
