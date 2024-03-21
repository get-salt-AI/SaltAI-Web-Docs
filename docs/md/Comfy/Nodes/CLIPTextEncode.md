# CLIP Text Encode (Prompt)
## Documentation
- Class name: `CLIPTextEncode`
- Category: `conditioning`
- Output node: `False`

The `CLIPTextEncode` node encodes text inputs using a CLIP model to produce conditioning information. It tokenizes the input text and then encodes these tokens to generate a conditioning vector and a pooled output, which are used for further processing or generation tasks.
## Input types
### Required
- **`text`**
    - The text input to be encoded. This is tokenized and encoded to produce the conditioning information.
    - Python dtype: `str`
    - Comfy dtype: `STRING`
- **`clip`**
    - The CLIP model used for text tokenization and encoding. It plays a crucial role in transforming the input text into a format suitable for generating conditioning information.
    - Python dtype: `torch.nn.Module`
    - Comfy dtype: `CLIP`
## Output types
- **`conditioning`**
    - The output conditioning information, consisting of a conditioning vector and a pooled output, derived from the encoded text. This information is crucial for guiding the generation process in tasks such as image synthesis.
    - Python dtype: `List[Tuple[torch.Tensor, Dict[str, torch.Tensor]]]`
    - Comfy dtype: `CONDITIONING`
## Usage tips
- Infra type: `GPU`
- Common nodes: `KSampler,ControlNetApplyAdvanced,KSampler //Inspire,SamplerCustom,Reroute,KSamplerAdvanced,ACN_AdvancedControlNetApply,ToBasicPipe,FaceDetailer`


## Source code
```python
class CLIPTextEncode:
    @classmethod
    def INPUT_TYPES(s):
        return {"required": {"text": ("STRING", {"multiline": True}), "clip": ("CLIP", )}}
    RETURN_TYPES = ("CONDITIONING",)
    FUNCTION = "encode"

    CATEGORY = "conditioning"

    def encode(self, clip, text):
        tokens = clip.tokenize(text)
        cond, pooled = clip.encode_from_tokens(tokens, return_pooled=True)
        return ([[cond, {"pooled_output": pooled}]], )

```
