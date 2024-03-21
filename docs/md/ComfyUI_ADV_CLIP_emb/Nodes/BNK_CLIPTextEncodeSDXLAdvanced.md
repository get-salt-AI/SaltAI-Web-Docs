# CLIP Text Encode SDXL (Advanced)
## Documentation
- Class name: `BNK_CLIPTextEncodeSDXLAdvanced`
- Category: `conditioning/advanced`
- Output node: `False`

This node performs advanced text encoding using the CLIP model, specifically designed for SDXL models. It encodes two types of text inputs (local and global) with customizable token normalization and weight interpretation strategies, and allows for balancing the influence of these texts. The output is a conditioning format suitable for further processing or generation tasks.
## Input types
### Required
- **`text_l`**
    - The local text input, which is expected to be more specific or detailed. It plays a crucial role in guiding the encoding process by providing fine-grained context.
    - Python dtype: `str`
    - Comfy dtype: `STRING`
- **`text_g`**
    - The global text input, providing a broader context or theme for the encoding. It complements the local text by offering a wider perspective that influences the overall encoding.
    - Python dtype: `str`
    - Comfy dtype: `STRING`
- **`clip`**
    - The CLIP model used for encoding the text inputs. It's a crucial component that transforms text into a format that can be utilized for further processing or generation.
    - Python dtype: `CLIP`
    - Comfy dtype: `CLIP`
- **`token_normalization`**
    - Defines the strategy for normalizing the tokens, affecting how the text is processed and encoded. It's essential for tailoring the encoding to specific needs or preferences.
    - Python dtype: `List[str]`
    - Comfy dtype: `['none', 'mean', 'length', 'length+mean']`
- **`weight_interpretation`**
    - Specifies how the weights are interpreted during encoding, influencing the emphasis on certain aspects of the text. It allows for fine-tuning the encoding process.
    - Python dtype: `List[str]`
    - Comfy dtype: `['comfy', 'A1111', 'compel', 'comfy++', 'down_weight']`
- **`balance`**
    - Adjusts the balance between the local and global text inputs, determining their relative influence on the final encoding. It's key for achieving the desired emphasis between detailed and broad contexts.
    - Python dtype: `float`
    - Comfy dtype: `FLOAT`
## Output types
- **`conditioning`**
    - The output is a conditioning format that includes the encoded texts and additional metadata, ready for further processing or generation tasks.
    - Python dtype: `List[Tuple[torch.Tensor, Dict[str, torch.Tensor]]]`
    - Comfy dtype: `CONDITIONING`
## Usage tips
- Infra type: `GPU`
- Common nodes: unknown


## Source code
```python
class AdvancedCLIPTextEncodeSDXL:
    @classmethod
    def INPUT_TYPES(s):
        return {"required": {
            "text_l": ("STRING", {"multiline": True}),
            "text_g": ("STRING", {"multiline": True}),
            "clip": ("CLIP", ),
            "token_normalization": (["none", "mean", "length", "length+mean"],),
            "weight_interpretation": (["comfy", "A1111", "compel", "comfy++", "down_weight"],),
            #"affect_pooled": (["disable", "enable"],),
            "balance": ("FLOAT", {"default": .5, "min": 0.0, "max": 1.0, "step": 0.01}),
            }}
    
    RETURN_TYPES = ("CONDITIONING",)
    FUNCTION = "encode"

    CATEGORY = "conditioning/advanced"

    def encode(self, clip, text_l, text_g, token_normalization, weight_interpretation, balance, affect_pooled='disable'):
        embeddings_final, pooled = advanced_encode_XL(clip, text_l, text_g, token_normalization, weight_interpretation, w_max=1.0, clip_balance=balance, apply_to_pooled=affect_pooled == "enable")
        return ([[embeddings_final, {"pooled_output": pooled}]], )

```
