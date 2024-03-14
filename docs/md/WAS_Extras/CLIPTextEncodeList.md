# CLIP Text Encode Sequence (Advanced)
## Documentation
- Class name: `CLIPTextEncodeList`
- Category: `conditioning`
- Output node: `False`

The node `CLIPTextEncodeList` is not explicitly defined in the provided context. However, based on the naming convention and the context of other CLIPTextEncode nodes, it can be inferred that if a `CLIPTextEncodeList` node existed, it would likely be involved in processing a list of text inputs through a CLIP model to generate encoded representations. These representations could then be used for various applications such as conditioning for generative models, text-based image editing, or enhancing text understanding in multimodal contexts.
## Input types
### Required
- **`clip`**
    - The CLIP model used for encoding the text inputs.
    - Python dtype: `sd1_clip.SDClipModel`
    - Comfy dtype: `CLIP`
- **`token_normalization`**
    - Specifies the method for normalizing tokens.
    - Python dtype: `str`
    - Comfy dtype: `STRING`
- **`weight_interpretation`**
    - Defines how weights are interpreted in the encoding process.
    - Python dtype: `str`
    - Comfy dtype: `STRING`
- **`text`**
    - A list of text inputs to be processed.
    - Python dtype: `List[str]`
    - Comfy dtype: `STRING`
## Output types
- **`conditioning_seq`**
    - The encoded representations of the text inputs.
    - Python dtype: `List[EncodedRepresentation]`
    - Comfy dtype: `CONDITIONING_SEQ`
## Usage tips
- Infra type: `GPU`
- Common nodes: unknown

The `CLIPTextEncodeList` node is primarily utilized for encoding a list of text inputs using a CLIP model, producing encoded representations suitable for tasks like text-based image editing, enhancing multimodal text understanding, or conditioning for generative models. It is often used with conditioning nodes to provide detailed textual context for image generation processes.
## Source code
```python
class CLIPTextEncodeSequence:
    @classmethod
    def INPUT_TYPES(s):
        return {
            "required": {
                "clip": ("CLIP", ),
                "token_normalization": (["none", "mean", "length", "length+mean"],),
                "weight_interpretation": (["comfy", "A1111", "compel", "comfy++"],),
                "text": ("STRING", {"multiline": True, "default": '''0:A portrait of a rosebud
5:A portrait of a blooming rosebud
10:A portrait of a blooming rose
15:A portrait of a rose'''}),
                }
            }
        
    RETURN_TYPES = ("CONDITIONING_SEQ",)
    RETURN_NAMES = ("conditioning_sequence",)
    IS_LIST_OUTPUT = (True,)

    FUNCTION = "encode"
    CATEGORY = "conditioning"

    def encode(self, clip, text, token_normalization, weight_interpretation):
        text = text.strip()
        conditionings = []
        for l in text.splitlines():
            match = re.match(r'(\d+):', l)
            
            if match:
                idx = int(match.group(1))
                _, line = l.split(":", 1)
                line = line.strip()
                
                if USE_BLK:
                    encoded = blk_adv.encode(clip=clip, text=line, token_normalization=token_normalization, weight_interpretation=weight_interpretation)
                else:
                    encoded = CLIPTextEncode.encode(clip=clip, text=line)
                
                conditioning = (idx, [encoded[0][0][0], encoded[0][0][1]])
                conditionings.append(conditioning)

        return (conditionings, )

```
