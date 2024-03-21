# AnimateDiff Loader 🎭🅐🅓①
## Documentation
- Class name: `ADE_AnimateDiffLoaderGen1`
- Category: `Animate Diff 🎭🅐🅓/① Gen1 nodes ①`
- Output node: `False`

This node is responsible for loading the first generation of AnimateDiff models. It facilitates the integration of these models into the AnimateDiff framework, allowing for the animation and manipulation of images based on the loaded model.
## Input types
### Required
- **`model`**
    - unknown
    - Python dtype: `unknown`
    - Comfy dtype: `MODEL`
- **`model_name`**
    - unknown
    - Python dtype: `unknown`
    - Comfy dtype: `['AnimateLCM_sd15_t2v.ckpt', 'mm-Stabilized_mid.pth', 'mm_sd_v15_v2.ckpt'...]`
- **`beta_schedule`**
    - unknown
    - Python dtype: `unknown`
    - Comfy dtype: `['autoselect', 'sqrt_linear (AnimateDiff)', 'linear (AnimateDiff-SDXL)'...]`
### Optional
- **`context_options`**
    - unknown
    - Python dtype: `unknown`
    - Comfy dtype: `CONTEXT_OPTIONS`
- **`motion_lora`**
    - unknown
    - Python dtype: `unknown`
    - Comfy dtype: `MOTION_LORA`
- **`ad_settings`**
    - unknown
    - Python dtype: `unknown`
    - Comfy dtype: `AD_SETTINGS`
- **`ad_keyframes`**
    - unknown
    - Python dtype: `unknown`
    - Comfy dtype: `AD_KEYFRAMES`
- **`sample_settings`**
    - unknown
    - Python dtype: `unknown`
    - Comfy dtype: `SAMPLE_SETTINGS`
- **`scale_multival`**
    - unknown
    - Python dtype: `unknown`
    - Comfy dtype: `MULTIVAL`
- **`effect_multival`**
    - unknown
    - Python dtype: `unknown`
    - Comfy dtype: `MULTIVAL`
## Output types
- **`model`**
    - The loaded instance of the AnimateDiff Gen1 model. This instance is ready for use in animating and manipulating images within the AnimateDiff framework.
    - Python dtype: `AnimateDiffModelGen1`
    - Comfy dtype: `MODEL`
## Usage tips
- Infra type: `GPU`
- Common nodes: `KSampler`


## Source code
```python
class AnimateDiffLoaderGen1:
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
                "ad_keyframes": ("AD_KEYFRAMES",),
                "sample_settings": ("SAMPLE_SETTINGS",),
                "scale_multival": ("MULTIVAL",),
                "effect_multival": ("MULTIVAL",),
            }
        }

    RETURN_TYPES = ("MODEL",)
    CATEGORY = "Animate Diff 🎭🅐🅓/① Gen1 nodes ①"
    FUNCTION = "load_mm_and_inject_params"

    def load_mm_and_inject_params(self,
        model: ModelPatcher,
        model_name: str, beta_schedule: str,# apply_mm_groupnorm_hack: bool,
        context_options: ContextOptionsGroup=None, motion_lora: MotionLoraList=None, ad_settings: AnimateDiffSettings=None,
        sample_settings: SampleSettings=None, scale_multival=None, effect_multival=None, ad_keyframes: ADKeyframeGroup=None,
    ):
        # load motion module and motion settings, if included
        motion_model = load_motion_module_gen2(model_name=model_name, motion_model_settings=ad_settings)
        # confirm that it is compatible with SD model
        validate_model_compatibility_gen2(model=model, motion_model=motion_model)
        # apply motion model to loaded_mm
        if motion_lora is not None:
            for lora in motion_lora.loras:
                load_motion_lora_as_patches(motion_model, lora)
        motion_model.scale_multival = scale_multival
        motion_model.effect_multival = effect_multival
        motion_model.keyframes = ad_keyframes.clone() if ad_keyframes else ADKeyframeGroup()
        
        # create injection params
        params = InjectionParams(unlimited_area_hack=False, model_name=motion_model.model.mm_info.mm_name)
        # apply context options
        if context_options:
            params.set_context(context_options)

        # set motion_scale and motion_model_settings
        if not ad_settings:
            ad_settings = AnimateDiffSettings()
        ad_settings.attn_scale = 1.0
        params.set_motion_model_settings(ad_settings)

        # backwards compatibility to support old way of masking scale
        if params.motion_model_settings.mask_attn_scale is not None:
            motion_model.scale_multival = get_combined_multival(scale_multival, (params.motion_model_settings.mask_attn_scale * params.motion_model_settings.attn_scale))
        
        # need to use a ModelPatcher that supports injection of motion modules into unet
        # need to use a ModelPatcher that supports injection of motion modules into unet
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
