# Load AnimateDiff Model 🎭🅐🅓②
## Documentation
- Class name: `ADE_LoadAnimateDiffModel`
- Category: `Animate Diff 🎭🅐🅓/② Gen2 nodes ②`
- Output node: `False`

This node is responsible for loading the AnimateDiff model into the system. It plays a crucial role in initializing the animation process by ensuring that the necessary motion model is correctly loaded and ready for use.
## Input types
### Required
- **`model_name`**
    - Specifies the name of the motion model to be loaded, which is essential for defining the animation behavior and characteristics. The model name is selected from a predefined list of available motion models.
    - Python dtype: `str`
    - Comfy dtype: `STRING`
### Optional
- **`ad_settings`**
    - Optional parameter for providing additional settings to the motion model, enhancing its flexibility and control over the animation.
    - Python dtype: `AnimateDiffSettings or None`
    - Comfy dtype: `MODEL`
## Output types
- **`motion_model_ade`**
    - The loaded AnimateDiff model, ready for animation application.
    - Python dtype: `MotionModelPatcher`
    - Comfy dtype: `MOTION_MODEL_ADE`
## Usage tips
- Infra type: `GPU`
- Common nodes: `ADE_ApplyAnimateDiffModel`


## Source code
```python
class LoadAnimateDiffModelNode:
    @classmethod
    def INPUT_TYPES(s):
        return {
            "required": {
                "model_name": (get_available_motion_models(),),
            },
            "optional": {
                "ad_settings": ("AD_SETTINGS",),
            }
        }

    RETURN_TYPES = ("MOTION_MODEL_ADE",)
    RETURN_NAMES = ("MOTION_MODEL",)
    CATEGORY = "Animate Diff 🎭🅐🅓/② Gen2 nodes ②"
    FUNCTION = "load_motion_model"

    def load_motion_model(self, model_name: str, ad_settings: AnimateDiffSettings=None):
        # load motion module and motion settings, if included
        motion_model = load_motion_module_gen2(model_name=model_name, motion_model_settings=ad_settings)
        return (motion_model,)

```
