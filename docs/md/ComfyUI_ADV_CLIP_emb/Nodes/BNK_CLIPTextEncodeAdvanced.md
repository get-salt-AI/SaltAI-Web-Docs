# CLIP Text Encode (Advanced)
## Documentation
- Class name: `BNK_CLIPTextEncodeAdvanced`
- Category: `conditioning/advanced`
- Output node: `False`

This node performs advanced text encoding using the CLIP model. It processes input text to generate embeddings and pooled output, considering token normalization and weight interpretation strategies.
## Input types
### Required
- **`text`**
    - The text input to be encoded. Supports multiline input, allowing for more complex and detailed text data to be processed.
    - Python dtype: `str`
    - Comfy dtype: `STRING`
- **`clip`**
    - The CLIP model used for encoding the text. It plays a crucial role in generating the embeddings and pooled output.
    - Python dtype: `CLIP`
    - Comfy dtype: `CLIP`
- **`token_normalization`**
    - Defines the method for normalizing the tokens extracted from the text. It affects the final embeddings by adjusting their scale or distribution.
    - Python dtype: `List[str]`
    - Comfy dtype: `['none', 'mean', 'length', 'length+mean']`
- **`weight_interpretation`**
    - Specifies how the weights are interpreted during the encoding process, influencing the emphasis on certain aspects of the text.
    - Python dtype: `List[str]`
    - Comfy dtype: `['comfy', 'A1111', 'compel', 'comfy++', 'down_weight']`
## Output types
- **`conditioning`**
    - The output includes embeddings and pooled output, structured for further processing or conditioning tasks.
    - Python dtype: `Tuple[List[Tuple[torch.Tensor, Dict[str, torch.Tensor]]]]`
    - Comfy dtype: `CONDITIONING`
## Usage tips
- Infra type: `GPU`
- Common nodes: `KSampler,SamplerCustom,ControlNetApplyAdvanced`


## Source code
```python
class AdvancedCLIPTextEncode:
    @classmethod
    def INPUT_TYPES(s):
        return {"required": {
            "text": ("STRING", {"multiline": True}),
            "clip": ("CLIP", ),
            "token_normalization": (["none", "mean", "length", "length+mean"],),
            "weight_interpretation": (["comfy", "A1111", "compel", "comfy++" ,"down_weight"],),
            #"affect_pooled": (["disable", "enable"],),
            }}
    
    RETURN_TYPES = ("CONDITIONING",)
    FUNCTION = "encode"

    CATEGORY = "conditioning/advanced"

    def encode(self, clip, text, token_normalization, weight_interpretation, affect_pooled='disable'):
        embeddings_final, pooled = advanced_encode(clip, text, token_normalization, weight_interpretation, w_max=1.0, apply_to_pooled=affect_pooled=='enable')
        return ([[embeddings_final, {"pooled_output": pooled}]], )

```
