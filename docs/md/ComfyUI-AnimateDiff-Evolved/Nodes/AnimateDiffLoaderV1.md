# AnimateDiff Loader [DEPRECATED] 🎭🅐🅓
## Documentation
- Class name: `AnimateDiffLoaderV1`
- Category: ``
- Output node: `False`

The `AnimateDiffLoaderV1` node is a deprecated component designed for loading AnimateDiff models. Despite its deprecated status, it played a crucial role in initializing and preparing AnimateDiff models for animation tasks, ensuring compatibility and proper setup for subsequent processing steps.
## Input types
### Required
- **`model`**
    - Specifies the model to be loaded for animation tasks. This parameter is essential for identifying the specific AnimateDiff model to be utilized.
    - Python dtype: `AnimateDiffModel`
    - Comfy dtype: `MODEL`
- **`latents`**
    - Defines the latent space to be used in conjunction with the AnimateDiff model. This parameter is crucial for the model's operation within its designated latent space.
    - Python dtype: `LatentSpace`
    - Comfy dtype: `LATENT`
- **`model_name`**
    - Indicates the name of the AnimateDiff model to be loaded. This parameter helps in selecting the appropriate model from available options.
    - Python dtype: `str`
    - Comfy dtype: `['STRING']`
- **`unlimited_area_hack`**
    - A boolean flag that enables or disables the unlimited area hack, affecting the model's operational scope.
    - Python dtype: `bool`
    - Comfy dtype: `BOOLEAN`
- **`beta_schedule`**
    - Specifies the beta schedule to be used, influencing the model's behavior and performance. It selects from a list of predefined beta schedules.
    - Python dtype: `str`
    - Comfy dtype: `['STRING']`
## Output types
- **`model`**
    - The loaded AnimateDiff model, ready for use in animation tasks. This output is essential for further processing and application of the model in generating animations.
    - Python dtype: `AnimateDiffModel`
    - Comfy dtype: `MODEL`
- **`latent`**
    - The latent space associated with the loaded AnimateDiff model, crucial for its operation and animation generation.
    - Python dtype: `LatentSpace`
    - Comfy dtype: `LATENT`
## Usage tips
- Infra type: `CPU`
- Common nodes: unknown


## Source code
```python
class AnimateDiffLoader_Deprecated:
    @classmethod
    def INPUT_TYPES(s):
        return {
            "required": {
                "model": ("MODEL",),
                "latents": ("LATENT",),
                "model_name": (get_available_motion_models(),),
                "unlimited_area_hack": ("BOOLEAN", {"default": False},),
                "beta_schedule": (BetaSchedules.get_alias_list_with_first_element(BetaSchedules.SQRT_LINEAR),),
            },
        }

    RETURN_TYPES = ("MODEL", "LATENT")
    CATEGORY = ""
    FUNCTION = "load_mm_and_inject_params"

    def load_mm_and_inject_params(
        self,
        model: ModelPatcher,
        latents: Dict[str, torch.Tensor],
        model_name: str, unlimited_area_hack: bool, beta_schedule: str,
    ):
        # load motion module
        motion_model = load_motion_module_gen1(model_name, model)
        # get total frames
        init_frames_len = len(latents["samples"])  # deprecated - no longer used for anything lol
        # set injection params
        params = InjectionParams(
                unlimited_area_hack=unlimited_area_hack,
                apply_mm_groupnorm_hack=True,
                model_name=model_name,
                apply_v2_properly=False,
        )
        # inject for use in sampling code
        model = ModelPatcherAndInjector(model)
        model.motion_models = MotionModelGroup(motion_model)
        model.motion_injection_params = params

        # save model sampling from BetaSchedule as object patch
        # if autoselect, get suggested beta_schedule from motion model
        if beta_schedule == BetaSchedules.AUTOSELECT and not model.motion_models.is_empty():
            beta_schedule = model.motion_models[0].model.get_best_beta_schedule(log=True)
        new_model_sampling = BetaSchedules.to_model_sampling(beta_schedule, model)
        if new_model_sampling is not None:
            model.add_object_patch("model_sampling", new_model_sampling)

        del motion_model
        return (model, latents)

```
