# Use Evolved Sampling 🎭🅐🅓②
## Documentation
- Class name: `ADE_UseEvolvedSampling`
- Category: `Animate Diff 🎭🅐🅓/② Gen2 nodes ②`
- Output node: `False`

The `ADE_UseEvolvedSampling` node utilizes an evolved sampling method to enhance the animation diffusion process. It integrates advanced sampling techniques with the animation diffusion model to generate more refined and accurate animations. This node is part of the second generation of AnimateDiff nodes, indicating its use of updated methodologies for improved performance.
## Input types
### Required
- **`model`**
    - Specifies the model to be used for evolved sampling. It is crucial for defining the behavior and capabilities of the sampling process.
    - Python dtype: `ModelPatcher`
    - Comfy dtype: `MODEL`
- **`beta_schedule`**
    - Determines the schedule of beta values used during the sampling process. This parameter influences the variance of the noise added at each step, affecting the quality and characteristics of the generated animation.
    - Python dtype: `BetaSchedules`
    - Comfy dtype: `STRING`
### Optional
- **`m_models`**
    - Optional. Refers to a group of motion models that may be used to enhance the sampling process with motion-specific information.
    - Python dtype: `MotionModelGroup`
    - Comfy dtype: `M_MODELS`
- **`context_options`**
    - Optional. Specifies the context options group, which can include various settings that affect how the context is managed and utilized during the sampling process.
    - Python dtype: `ContextOptionsGroup`
    - Comfy dtype: `CONTEXT_OPTIONS`
- **`sample_settings`**
    - Optional. Defines the sample settings to be used during the evolved sampling process. These settings can include parameters such as noise type and seed generation options.
    - Python dtype: `SampleSettings`
    - Comfy dtype: `SAMPLE_SETTINGS`
## Output types
- **`model`**
    - Outputs the model with evolved sampling applied, ready for further use in the animation diffusion process.
    - Python dtype: `ModelPatcher`
    - Comfy dtype: `MODEL`
## Usage tips
- Infra type: `GPU`
- Common nodes: `KSampler,ToBasicPipe,SamplerCustom,LCMScheduler`


## Source code
```python
class UseEvolvedSamplingNode:
    @classmethod
    def INPUT_TYPES(s):
        return {
            "required": {
                "model": ("MODEL",),
                "beta_schedule": (BetaSchedules.ALIAS_LIST, {"default": BetaSchedules.AUTOSELECT}),
            },
            "optional": {
                "m_models": ("M_MODELS",),
                "context_options": ("CONTEXT_OPTIONS",),
                "sample_settings": ("SAMPLE_SETTINGS",),
                #"beta_schedule_override": ("BETA_SCHEDULE",),
            }
        }
    
    RETURN_TYPES = ("MODEL",)
    CATEGORY = "Animate Diff 🎭🅐🅓/② Gen2 nodes ②"
    FUNCTION = "use_evolved_sampling"

    def use_evolved_sampling(self, model: ModelPatcher, beta_schedule: str, m_models: MotionModelGroup=None, context_options: ContextOptionsGroup=None,
                             sample_settings: SampleSettings=None, beta_schedule_override=None):
        if m_models is not None:
            m_models = m_models.clone()
            # for each motion model, confirm that it is compatible with SD model
            for motion_model in m_models.models:
                validate_model_compatibility_gen2(model=model, motion_model=motion_model)
            # create injection params
            model_name_list = [motion_model.model.mm_info.mm_name for motion_model in m_models.models]
            model_names = ",".join(model_name_list)
            # TODO: check if any apply_v2_properly is set to False
            params = InjectionParams(unlimited_area_hack=False, model_name=model_names)
        else:
            params = InjectionParams()
        # apply context options
        if context_options:
            params.set_context(context_options)
        # need to use a ModelPatcher that supports injection of motion modules into unet
        model = ModelPatcherAndInjector(model)
        model.motion_models = m_models
        model.sample_settings = sample_settings if sample_settings is not None else SampleSettings()
        model.motion_injection_params = params

        # save model_sampling from BetaSchedule as object patch
        # if autoselect, get suggested beta_schedule from motion model
        if beta_schedule == BetaSchedules.AUTOSELECT and not model.motion_models.is_empty():
            beta_schedule = model.motion_models[0].model.get_best_beta_schedule(log=True)
        new_model_sampling = BetaSchedules.to_model_sampling(beta_schedule, model)
        if new_model_sampling is not None:
            model.add_object_patch("model_sampling", new_model_sampling)
        
        del m_models
        return (model,)

```
