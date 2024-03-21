# unCLIPConditioning
## Documentation
- Class name: `unCLIPConditioning`
- Category: `conditioning`
- Output node: `False`

This node applies additional conditioning to the input conditioning data based on CLIP vision output, strength, and noise augmentation parameters. It enhances the conditioning by incorporating visual features and adjustable parameters to influence the generation process.
## Input types
### Required
- **`conditioning`**
    - The base conditioning data to which the enhancements are applied. It serves as the foundation for further modifications.
    - Python dtype: `List[Tuple[Any, Dict[str, Any]]]`
    - Comfy dtype: `CONDITIONING`
- **`clip_vision_output`**
    - The output from a CLIP vision model, used to enrich the conditioning with visual features.
    - Python dtype: `CLIPVisionOutput`
    - Comfy dtype: `CLIP_VISION_OUTPUT`
- **`strength`**
    - Determines the intensity of the applied enhancements, allowing for fine-tuning of the conditioning's influence.
    - Python dtype: `float`
    - Comfy dtype: `FLOAT`
- **`noise_augmentation`**
    - Specifies the level of noise augmentation to apply, adding variability to the conditioning.
    - Python dtype: `float`
    - Comfy dtype: `FLOAT`
## Output types
- **`conditioning`**
    - The enhanced conditioning data, ready for use in further processing or generation tasks.
    - Python dtype: `List[Tuple[Any, Dict[str, Any]]]`
    - Comfy dtype: `CONDITIONING`
## Usage tips
- Infra type: `CPU`
- Common nodes: `ControlNetApplyAdvanced,ConditioningConcat`


## Source code
```python
class unCLIPConditioning:
    @classmethod
    def INPUT_TYPES(s):
        return {"required": {"conditioning": ("CONDITIONING", ),
                             "clip_vision_output": ("CLIP_VISION_OUTPUT", ),
                             "strength": ("FLOAT", {"default": 1.0, "min": -10.0, "max": 10.0, "step": 0.01}),
                             "noise_augmentation": ("FLOAT", {"default": 0.0, "min": 0.0, "max": 1.0, "step": 0.01}),
                             }}
    RETURN_TYPES = ("CONDITIONING",)
    FUNCTION = "apply_adm"

    CATEGORY = "conditioning"

    def apply_adm(self, conditioning, clip_vision_output, strength, noise_augmentation):
        if strength == 0:
            return (conditioning, )

        c = []
        for t in conditioning:
            o = t[1].copy()
            x = {"clip_vision_output": clip_vision_output, "strength": strength, "noise_augmentation": noise_augmentation}
            if "unclip_conditioning" in o:
                o["unclip_conditioning"] = o["unclip_conditioning"][:] + [x]
            else:
                o["unclip_conditioning"] = [x]
            n = [t[0], o]
            c.append(n)
        return (c, )

```
