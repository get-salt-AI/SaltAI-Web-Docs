# Load AnimateDiff LoRA 🎭🅐🅓
## Documentation
- Class name: `ADE_AnimateDiffLoRALoader`
- Category: `Animate Diff 🎭🅐🅓`
- Output node: `False`

The `ADE_AnimateDiffLoRALoader` node is responsible for loading motion LoRA (Locally Reweighted Affinity) models, which are used to animate static images by applying predefined or custom motion patterns. It supports loading a specific LoRA model by name, adjusting its strength for the animation effect, and optionally chaining it with previously loaded LoRA models to create complex animations.
## Input types
### Required
- **`lora_name`**
    - Specifies the name of the LoRA model to be loaded. This is crucial for determining the specific motion pattern to apply to the static image.
    - Python dtype: `str`
    - Comfy dtype: `STRING`
- **`strength`**
    - Adjusts the intensity of the motion effect applied by the LoRA model. A higher value results in a more pronounced animation.
    - Python dtype: `float`
    - Comfy dtype: `FLOAT`
### Optional
- **`prev_motion_lora`**
    - Allows for chaining multiple LoRA models by providing previously loaded models. This enables the creation of complex animations by combining different motion patterns.
    - Python dtype: `MotionLoraList`
    - Comfy dtype: `MOTION_LORA`
## Output types
- **`motion_lora`**
    - Returns the updated list of LoRA models, including the newly loaded model, ready to be used for animating images.
    - Python dtype: `MotionLoraList`
    - Comfy dtype: `MOTION_LORA`
## Usage tips
- Infra type: `CPU`
- Common nodes: `ADE_AnimateDiffLoaderWithContext,ADE_AnimateDiffLoRALoader`


## Source code
```python
class AnimateDiffLoraLoader:
    @classmethod
    def INPUT_TYPES(s):
        return {
            "required": {
                "lora_name": (get_available_motion_loras(),),
                "strength": ("FLOAT", {"default": 1.0, "min": 0.0, "max": 10.0, "step": 0.001}),
            },
            "optional": {
                "prev_motion_lora": ("MOTION_LORA",),
            }
        }
    
    RETURN_TYPES = ("MOTION_LORA",)
    CATEGORY = "Animate Diff 🎭🅐🅓"
    FUNCTION = "load_motion_lora"

    def load_motion_lora(self, lora_name: str, strength: float, prev_motion_lora: MotionLoraList=None):
        if prev_motion_lora is None:
            prev_motion_lora = MotionLoraList()
        else:
            prev_motion_lora = prev_motion_lora.clone()
        # check if motion lora with name exists
        lora_path = get_motion_lora_path(lora_name)
        if not Path(lora_path).is_file():
            raise FileNotFoundError(f"Motion lora with name '{lora_name}' not found.")
        # create motion lora info to be loaded in AnimateDiff Loader
        lora_info = MotionLoraInfo(name=lora_name, strength=strength)
        prev_motion_lora.add_lora(lora_info)

        return (prev_motion_lora,)

```
