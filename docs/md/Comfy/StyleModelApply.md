# Apply Style Model
## Documentation
- Class name: `StyleModelApply`
- Category: `conditioning/style_model`
- Output node: `False`

This node applies a style model to a given conditioning, enhancing or altering its style based on the output of a CLIP vision model. It integrates the style model's conditions into the conditioning, preparing it for further processing or generation tasks.
## Input types
### Required
- **`conditioning`**
    - The base conditioning data to which the style will be applied. It's crucial for defining the initial context or content that will be styled.
    - Python dtype: `List[Tuple[torch.Tensor, Dict[str, Any]]]`
    - Comfy dtype: `CONDITIONING`
- **`style_model`**
    - The style model used to generate style conditions based on the CLIP vision model's output. It plays a key role in determining the final style applied to the conditioning.
    - Python dtype: `StyleModel`
    - Comfy dtype: `STYLE_MODEL`
- **`clip_vision_output`**
    - The output from a CLIP vision model, which is used by the style model to generate style conditions. It's essential for tailoring the style to the specific content represented in the conditioning.
    - Python dtype: `torch.Tensor`
    - Comfy dtype: `CLIP_VISION_OUTPUT`
## Output types
- **`conditioning`**
    - The enhanced or altered conditioning data, now integrated with style conditions from the style model.
    - Python dtype: `List[Tuple[torch.Tensor, Dict[str, Any]]]`
    - Comfy dtype: `CONDITIONING`
## Usage tips
- Infra type: `GPU`
- Common nodes: unknown


## Source code
```python
class StyleModelApply:
    @classmethod
    def INPUT_TYPES(s):
        return {"required": {"conditioning": ("CONDITIONING", ),
                             "style_model": ("STYLE_MODEL", ),
                             "clip_vision_output": ("CLIP_VISION_OUTPUT", ),
                             }}
    RETURN_TYPES = ("CONDITIONING",)
    FUNCTION = "apply_stylemodel"

    CATEGORY = "conditioning/style_model"

    def apply_stylemodel(self, clip_vision_output, style_model, conditioning):
        cond = style_model.get_cond(clip_vision_output).flatten(start_dim=0, end_dim=1).unsqueeze(dim=0)
        c = []
        for t in conditioning:
            n = [torch.cat((t[0], cond), dim=1), t[1].copy()]
            c.append(n)
        return (c, )

```
