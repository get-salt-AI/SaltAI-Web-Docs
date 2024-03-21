# AnimateDiff Loader [Legacy] 🎭🅐🅓①
## Documentation
- Class name: `ADE_AnimateDiffLoaderWithContext`
- Category: `Animate Diff 🎭🅐🅓/① Gen1 nodes ①`
- Output node: `False`

This node is designed to load AnimateDiff models in a legacy context, allowing for the integration of AnimateDiff functionalities with existing workflows or systems that were built around earlier versions of the AnimateDiff framework. It ensures compatibility and facilitates the transition to newer versions without disrupting established processes.
## Input types
### Required
- **`model`**
    - Specifies the model to be loaded for animation. This parameter is essential for defining the base upon which the AnimateDiff functionalities will be applied.
    - Python dtype: `BaseModel`
    - Comfy dtype: `MODEL`
- **`model_name`**
    - Determines the specific motion model to be utilized in the animation process. It is crucial for identifying and loading the correct AnimateDiff model.
    - Python dtype: `str`
    - Comfy dtype: `STRING`
- **`beta_schedule`**
    - Defines the beta schedule to be used in the AnimateDiff model. This parameter influences the animation's temporal dynamics.
    - Python dtype: `str`
    - Comfy dtype: `STRING`
### Optional
- **`context_options`**
    - Optional. Specifies additional context options for the AnimateDiff model, allowing for further customization of the animation.
    - Python dtype: `ContextOptions or None`
    - Comfy dtype: `CONTEXT_OPTIONS`
- **`motion_lora`**
    - Optional. Allows for the inclusion of motion LoRA (Locally Recurrent Architecture) for enhanced motion effects in the animation.
    - Python dtype: `MotionLoraList or None`
    - Comfy dtype: `MOTION_LORA`
- **`ad_settings`**
    - Optional. Specifies AnimateDiff settings for customizing the animation process.
    - Python dtype: `AnimateDiffSettings or None`
    - Comfy dtype: `AD_SETTINGS`
- **`sample_settings`**
    - Optional. Defines the sampling settings for the animation, influencing the generation process.
    - Python dtype: `SampleSettings or None`
    - Comfy dtype: `SAMPLE_SETTINGS`
- **`motion_scale`**
    - Optional. Adjusts the scale of motion applied in the animation, allowing for control over the intensity of motion effects.
    - Python dtype: `float or None`
    - Comfy dtype: `FLOAT`
- **`apply_v2_models_properly`**
    - Optional. A flag indicating whether to apply version 2 models correctly, ensuring compatibility with newer AnimateDiff functionalities.
    - Python dtype: `bool or None`
    - Comfy dtype: `BOOLEAN`
- **`ad_keyframes`**
    - Optional. Specifies keyframes for the animation, enabling precise control over motion effects at specific points in the animation timeline.
    - Python dtype: `ADKeyframeGroup or None`
    - Comfy dtype: `AD_KEYFRAMES`
## Output types
- **`model`**
    - Returns the loaded model with AnimateDiff parameters injected, ready for animation.
    - Python dtype: `BaseModel`
    - Comfy dtype: `MODEL`
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
