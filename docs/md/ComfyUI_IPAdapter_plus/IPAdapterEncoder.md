# Encode IPAdapter Image
## Documentation
- Class name: `IPAdapterEncoder`
- Category: `ipadapter`
- Output node: `False`

The `IPAdapterEncoder` node is designed for encoding images using the IPAdapter mechanism. It encapsulates the process of transforming images into a format suitable for further processing or integration with other models.
## Input types
### Required
- **`clip_vision`**
    - This input represents the CLIP vision model used for image understanding and feature extraction. It's crucial for interpreting the content of the images to be encoded.
    - Python dtype: `torch.nn.Module`
    - Comfy dtype: `CLIP_VISION`
- **`image_1`**
    - The primary image to be processed. It serves as the main subject for the encoding operation, influencing the outcome significantly.
    - Python dtype: `torch.Tensor`
    - Comfy dtype: `IMAGE`
- **`ipadapter_plus`**
    - A boolean flag indicating whether to apply an enhanced IPAdapter process. This option can alter the encoding strategy, affecting the complexity and detail of the output.
    - Python dtype: `bool`
    - Comfy dtype: `BOOLEAN`
- **`noise`**
    - A float value representing the amount of noise to introduce during the encoding process. This can affect the robustness and variability of the encoded embeddings.
    - Python dtype: `float`
    - Comfy dtype: `FLOAT`
- **`weight_1`**
    - unknown
    - Python dtype: `unknown`
    - Comfy dtype: `FLOAT`
### Optional
- **`image_2`**
    - unknown
    - Python dtype: `unknown`
    - Comfy dtype: `IMAGE`
- **`image_3`**
    - unknown
    - Python dtype: `unknown`
    - Comfy dtype: `IMAGE`
- **`image_4`**
    - unknown
    - Python dtype: `unknown`
    - Comfy dtype: `IMAGE`
- **`weight_2`**
    - unknown
    - Python dtype: `unknown`
    - Comfy dtype: `FLOAT`
- **`weight_3`**
    - unknown
    - Python dtype: `unknown`
    - Comfy dtype: `FLOAT`
- **`weight_4`**
    - unknown
    - Python dtype: `unknown`
    - Comfy dtype: `FLOAT`
## Output types
- **`embeds`**
    - The result of the encoding process, which combines the input embeddings into a new set of embeddings suitable for further processing.
    - Python dtype: `torch.Tensor`
    - Comfy dtype: `EMBEDS`
## Usage tips
- Infra type: `GPU`
- Common nodes: `IPAdapterApplyEncoded`

The IPAdapterEncoder node is crucial for encoding images using the IPAdapter mechanism, taking in primary and optional additional images along with a CLIP vision input to produce enhanced embeddings suitable for further AI-driven image processing and manipulation. This node is particularly useful in pipelines like SMOOSH v2 for integrating advanced image processing techniques, thereby enhancing the AI's understanding and manipulation of images based on the provided inputs.
## Source code
```python
class IPAdapterEncoder:
    @classmethod
    def INPUT_TYPES(s):
        return {"required": {
            "clip_vision": ("CLIP_VISION",),
            "image_1": ("IMAGE",),
            "ipadapter_plus": ("BOOLEAN", { "default": False }),
            "noise": ("FLOAT", { "default": 0.0, "min": 0.0, "max": 1.0, "step": 0.01 }),
            "weight_1": ("FLOAT", { "default": 1.0, "min": 0, "max": 1.0, "step": 0.01 }),
            },
            "optional": {
                "image_2": ("IMAGE",),
                "image_3": ("IMAGE",),
                "image_4": ("IMAGE",),
                "weight_2": ("FLOAT", { "default": 1.0, "min": 0, "max": 1.0, "step": 0.01 }),
                "weight_3": ("FLOAT", { "default": 1.0, "min": 0, "max": 1.0, "step": 0.01 }),
                "weight_4": ("FLOAT", { "default": 1.0, "min": 0, "max": 1.0, "step": 0.01 }),
            }
        }

    RETURN_TYPES = ("EMBEDS",)
    FUNCTION = "preprocess"
    CATEGORY = "ipadapter"

    def preprocess(self, clip_vision, image_1, ipadapter_plus, noise, weight_1, image_2=None, image_3=None, image_4=None, weight_2=1.0, weight_3=1.0, weight_4=1.0):
        weight_1 *= (0.1 + (weight_1 - 0.1))
        weight_2 *= (0.1 + (weight_2 - 0.1))
        weight_3 *= (0.1 + (weight_3 - 0.1))
        weight_4 *= (0.1 + (weight_4 - 0.1))

        image = image_1
        weight = [weight_1]*image_1.shape[0]
        
        if image_2 is not None:
            if image_1.shape[1:] != image_2.shape[1:]:
                image_2 = comfy.utils.common_upscale(image_2.movedim(-1,1), image.shape[2], image.shape[1], "bilinear", "center").movedim(1,-1)
            image = torch.cat((image, image_2), dim=0)
            weight += [weight_2]*image_2.shape[0]
        if image_3 is not None:
            if image.shape[1:] != image_3.shape[1:]:
                image_3 = comfy.utils.common_upscale(image_3.movedim(-1,1), image.shape[2], image.shape[1], "bilinear", "center").movedim(1,-1)
            image = torch.cat((image, image_3), dim=0)
            weight += [weight_3]*image_3.shape[0]
        if image_4 is not None:
            if image.shape[1:] != image_4.shape[1:]:
                image_4 = comfy.utils.common_upscale(image_4.movedim(-1,1), image.shape[2], image.shape[1], "bilinear", "center").movedim(1,-1)
            image = torch.cat((image, image_4), dim=0)
            weight += [weight_4]*image_4.shape[0]
        
        clip_embed = clip_vision.encode_image(image)
        neg_image = image_add_noise(image, noise) if noise > 0 else None
        
        if ipadapter_plus:
            clip_embed = clip_embed.penultimate_hidden_states
            if noise > 0:
                clip_embed_zeroed = clip_vision.encode_image(neg_image).penultimate_hidden_states
            else:
                clip_embed_zeroed = zeroed_hidden_states(clip_vision, image.shape[0])
        else:
            clip_embed = clip_embed.image_embeds
            if noise > 0:
                clip_embed_zeroed = clip_vision.encode_image(neg_image).image_embeds
            else:
                clip_embed_zeroed = torch.zeros_like(clip_embed)

        if any(e != 1.0 for e in weight):
            weight = torch.tensor(weight).unsqueeze(-1) if not ipadapter_plus else torch.tensor(weight).unsqueeze(-1).unsqueeze(-1)
            clip_embed = clip_embed * weight
        
        output = torch.stack((clip_embed, clip_embed_zeroed))

        return( output, )

```
