# GLIGENTextBoxApply
## Documentation
- Class name: `GLIGENTextBoxApply`
- Category: `conditioning/gligen`
- Output node: `False`

This node applies text-based conditioning to a given set of conditioning parameters using a GLIGEN text box model. It encodes the provided text using a CLIP model, then modifies the conditioning parameters to include position-based information derived from the encoded text, dimensions, and specified position.
## Input types
### Required
- **`conditioning_to`**
    - The initial set of conditioning parameters to which the text-based conditioning will be applied. It serves as the base for further modifications.
    - Python dtype: `List[Tuple[torch.Tensor, Dict[str, Any]]]`
    - Comfy dtype: `CONDITIONING`
- **`clip`**
    - The CLIP model used for encoding the provided text into a format that can be utilized for conditioning.
    - Python dtype: `CLIP`
    - Comfy dtype: `CLIP`
- **`gligen_textbox_model`**
    - The GLIGEN model responsible for generating text boxes based on the encoded text and specified dimensions.
    - Python dtype: `GLIGEN`
    - Comfy dtype: `GLIGEN`
- **`text`**
    - The text to be encoded and applied as part of the conditioning. It supports multiline input.
    - Python dtype: `str`
    - Comfy dtype: `STRING`
- **`width`**
    - The width of the text box in pixels.
    - Python dtype: `int`
    - Comfy dtype: `INT`
- **`height`**
    - The height of the text box in pixels.
    - Python dtype: `int`
    - Comfy dtype: `INT`
- **`x`**
    - The x-coordinate of the top-left corner of the text box.
    - Python dtype: `int`
    - Comfy dtype: `INT`
- **`y`**
    - The y-coordinate of the top-left corner of the text box.
    - Python dtype: `int`
    - Comfy dtype: `INT`
## Output types
- **`conditioning`**
    - The modified set of conditioning parameters, now including the text-based conditioning.
    - Python dtype: `List[Tuple[torch.Tensor, Dict[str, Any]]]`
    - Comfy dtype: `CONDITIONING`
## Usage tips
- Infra type: `GPU`
- Common nodes: `KSampler,ToDetailerPipe`


## Source code
```python
class GLIGENTextBoxApply:
    @classmethod
    def INPUT_TYPES(s):
        return {"required": {"conditioning_to": ("CONDITIONING", ),
                              "clip": ("CLIP", ),
                              "gligen_textbox_model": ("GLIGEN", ),
                              "text": ("STRING", {"multiline": True}),
                              "width": ("INT", {"default": 64, "min": 8, "max": MAX_RESOLUTION, "step": 8}),
                              "height": ("INT", {"default": 64, "min": 8, "max": MAX_RESOLUTION, "step": 8}),
                              "x": ("INT", {"default": 0, "min": 0, "max": MAX_RESOLUTION, "step": 8}),
                              "y": ("INT", {"default": 0, "min": 0, "max": MAX_RESOLUTION, "step": 8}),
                             }}
    RETURN_TYPES = ("CONDITIONING",)
    FUNCTION = "append"

    CATEGORY = "conditioning/gligen"

    def append(self, conditioning_to, clip, gligen_textbox_model, text, width, height, x, y):
        c = []
        cond, cond_pooled = clip.encode_from_tokens(clip.tokenize(text), return_pooled="unprojected")
        for t in conditioning_to:
            n = [t[0], t[1].copy()]
            position_params = [(cond_pooled, height // 8, width // 8, y // 8, x // 8)]
            prev = []
            if "gligen" in n[1]:
                prev = n[1]['gligen'][2]

            n[1]['gligen'] = ("position", gligen_textbox_model, prev + position_params)
            c.append(n)
        return (c, )

```
