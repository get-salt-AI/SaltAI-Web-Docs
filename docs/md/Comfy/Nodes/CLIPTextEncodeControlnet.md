# CLIPTextEncodeControlnet
## Documentation
- Class name: `CLIPTextEncodeControlnet`
- Category: `_for_testing/conditioning`
- Output node: `False`

The `CLIPTextEncodeControlnet` node is designed for encoding text inputs using a CLIP model, specifically for control network applications. It tokenizes the input text, encodes it to obtain both the standard and pooled representations, and then integrates these representations into the provided conditioning data, enhancing it with additional control information.
## Input types
### Required
- **`clip`**
    - The CLIP model used for text tokenization and encoding. It's crucial for transforming the input text into a format that can be effectively utilized for further processing and analysis.
    - Python dtype: `torch.nn.Module`
    - Comfy dtype: `CLIP`
- **`conditioning`**
    - A list of conditioning data to which the encoded text representations are added. This process enriches the conditioning with text-derived features, enabling more controlled and nuanced generation processes.
    - Python dtype: `List[Tuple[Any, Dict[str, Any]]]`
    - Comfy dtype: `CONDITIONING`
- **`text`**
    - The input text to be encoded. This text is tokenized and encoded by the CLIP model, forming the basis for the control information added to the conditioning data.
    - Python dtype: `str`
    - Comfy dtype: `STRING`
## Output types
- **`conditioning`**
    - The enhanced conditioning data, now including the encoded text representations. This output is ready for use in controlled generation tasks, providing a richer set of features for the generation process.
    - Python dtype: `List[Tuple[Any, Dict[str, Any]]]`
    - Comfy dtype: `CONDITIONING`
## Usage tips
- Infra type: `GPU`
- Common nodes: unknown


## Source code
```python
class CLIPTextEncodeControlnet:
    @classmethod
    def INPUT_TYPES(s):
        return {"required": {"clip": ("CLIP", ), "conditioning": ("CONDITIONING", ), "text": ("STRING", {"multiline": True})}}
    RETURN_TYPES = ("CONDITIONING",)
    FUNCTION = "encode"

    CATEGORY = "_for_testing/conditioning"

    def encode(self, clip, conditioning, text):
        tokens = clip.tokenize(text)
        cond, pooled = clip.encode_from_tokens(tokens, return_pooled=True)
        c = []
        for t in conditioning:
            n = [t[0], t[1].copy()]
            n[1]['cross_attn_controlnet'] = cond
            n[1]['pooled_output_controlnet'] = pooled
            c.append(n)
        return (c, )

```
