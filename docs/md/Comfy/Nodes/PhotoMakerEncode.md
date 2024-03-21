# PhotoMakerEncode
## Documentation
- Class name: `PhotoMakerEncode`
- Category: `_for_testing/photomaker`
- Output node: `False`

The `PhotoMakerEncode` node is designed to enhance the embedding of text prompts by incorporating visual information from images. It processes image pixel values and text prompt embeddings, fusing them together to produce updated prompt embeddings that are enriched with visual context. This process involves projecting image embeddings into a shared space with the text embeddings and then combining them, aiming to improve the relevance and specificity of the generated content based on the visual input.
## Input types
### Required
- **`photomaker`**
    - Represents the model used for incorporating visual information into text prompt embeddings. It is essential for the process of enhancing text prompts with visual context.
    - Python dtype: `PhotoMakerIDEncoder`
    - Comfy dtype: `PHOTOMAKER`
- **`image`**
    - The input image whose visual information is to be fused with the text prompt embeddings. This visual context aims to enrich the text prompts, making them more relevant and specific.
    - Python dtype: `torch.Tensor`
    - Comfy dtype: `IMAGE`
- **`clip`**
    - The CLIP model used for processing the text and possibly the image. It plays a crucial role in encoding the text and generating embeddings that are later fused with visual information.
    - Python dtype: `CLIPModel`
    - Comfy dtype: `CLIP`
- **`text`**
    - The text prompt to be enhanced with visual information. The process aims to make the generated content more relevant and specific by incorporating visual context.
    - Python dtype: `str`
    - Comfy dtype: `STRING`
## Output types
- **`conditioning`**
    - The updated text prompt embeddings, enriched with visual information from the input images. This enhanced representation aims to improve the generated content's relevance and specificity based on the visual context.
    - Python dtype: `torch.Tensor`
    - Comfy dtype: `CONDITIONING`
## Usage tips
- Infra type: `GPU`
- Common nodes: unknown


## Source code
```python
class PhotoMakerEncode:
    @classmethod
    def INPUT_TYPES(s):
        return {"required": { "photomaker": ("PHOTOMAKER",),
                              "image": ("IMAGE",),
                              "clip": ("CLIP", ),
                              "text": ("STRING", {"multiline": True, "default": "photograph of photomaker"}),
                             }}

    RETURN_TYPES = ("CONDITIONING",)
    FUNCTION = "apply_photomaker"

    CATEGORY = "_for_testing/photomaker"

    def apply_photomaker(self, photomaker, image, clip, text):
        special_token = "photomaker"
        pixel_values = comfy.clip_vision.clip_preprocess(image.to(photomaker.load_device)).float()
        try:
            index = text.split(" ").index(special_token) + 1
        except ValueError:
            index = -1
        tokens = clip.tokenize(text, return_word_ids=True)
        out_tokens = {}
        for k in tokens:
            out_tokens[k] = []
            for t in tokens[k]:
                f = list(filter(lambda x: x[2] != index, t))
                while len(f) < len(t):
                    f.append(t[-1])
                out_tokens[k].append(f)

        cond, pooled = clip.encode_from_tokens(out_tokens, return_pooled=True)

        if index > 0:
            token_index = index - 1
            num_id_images = 1
            class_tokens_mask = [True if token_index <= i < token_index+num_id_images else False for i in range(77)]
            out = photomaker(id_pixel_values=pixel_values.unsqueeze(0), prompt_embeds=cond.to(photomaker.load_device),
                            class_tokens_mask=torch.tensor(class_tokens_mask, dtype=torch.bool, device=photomaker.load_device).unsqueeze(0))
        else:
            out = cond

        return ([[out, {"pooled_output": pooled}]], )

```
