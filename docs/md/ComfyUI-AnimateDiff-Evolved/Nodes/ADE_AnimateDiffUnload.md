# AnimateDiff Unload 🎭🅐🅓
## Documentation
- Class name: `ADE_AnimateDiffUnload`
- Category: `Animate Diff 🎭🅐🅓/extras`
- Output node: `False`

The `ADE_AnimateDiffUnload` node is designed to remove motion modules from a given model, effectively reverting it to its original, unmodified state. This operation is crucial for scenarios where the modifications applied by AnimateDiff need to be undone, ensuring that the model can be used in its standard form or prepared for a different set of modifications.
## Input types
### Required
- **`model`**
    - The `model` parameter represents the model from which motion modules will be unloaded. This is essential for reverting the model to its vanilla state, making it suitable for further operations or analyses.
    - Python dtype: `ModelPatcher`
    - Comfy dtype: `MODEL`
## Output types
- **`model`**
    - Returns a clone of the original model with motion modules removed, ensuring that the model is in its unmodified state and ready for further use or modifications.
    - Python dtype: `ModelPatcher`
    - Comfy dtype: `MODEL`
## Usage tips
- Infra type: `CPU`
- Common nodes: unknown


## Source code
```python
class AnimateDiffUnload:
    def __init__(self) -> None:
        self.change = IsChangedHelper()

    @classmethod
    def INPUT_TYPES(s):
        return {"required": {"model": ("MODEL",)}}

    RETURN_TYPES = ("MODEL",)
    CATEGORY = "Animate Diff 🎭🅐🅓/extras"
    FUNCTION = "unload_motion_modules"

    def unload_motion_modules(self, model: ModelPatcher):
        # return model clone with ejected params
        #model = eject_params_from_model(model)
        model = get_vanilla_model_patcher(model)
        return (model.clone(),)

```
