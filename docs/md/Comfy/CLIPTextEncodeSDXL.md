# CLIPTextEncodeSDXL
## Documentation
- Class name: `CLIPTextEncodeSDXL`
- Category: `advanced/conditioning`
- Output node: `False`

This node is not explicitly defined in the provided context, indicating it might be a part of a larger system or framework not fully detailed here. Given the naming convention and the context of other nodes, it can be inferred that CLIPTextEncodeSDXL likely involves encoding text inputs using a CLIP model specifically tailored or configured for the SDXL architecture. This process would involve transforming textual data into a format that is compatible with subsequent processing or analysis steps, particularly in tasks related to image-text interaction or multimodal learning.
## Input types
### Required
- **`width`**
    - Specifies the width of the target image. This dimension is crucial for ensuring that the encoded text is appropriately scaled and aligned with the visual content it is intended to interact with or generate.
    - Python dtype: `int`
    - Comfy dtype: `INT`
- **`height`**
    - Similar to the width parameter, the height specifies the vertical dimension of the target image. It plays a key role in matching the encoded text's representation with the intended visual context, affecting the overall coherence of the generated or modified image.
    - Python dtype: `int`
    - Comfy dtype: `INT`
- **`crop_w`**
    - Specifies the width of the crop area. This parameter is used to define a specific region of interest within the image that the text encoding should focus on.
    - Python dtype: `int`
    - Comfy dtype: `INT`
- **`crop_h`**
    - Specifies the height of the crop area. Similar to crop_w, it defines a vertical region of interest within the image for the text encoding process.
    - Python dtype: `int`
    - Comfy dtype: `INT`
- **`target_width`**
    - Defines the desired width of the output image after processing. This parameter can influence the scaling and aspect ratio of the final image.
    - Python dtype: `int`
    - Comfy dtype: `INT`
- **`target_height`**
    - Defines the desired height of the output image after processing. It works alongside target_width to determine the final dimensions of the image.
    - Python dtype: `int`
    - Comfy dtype: `INT`
- **`text_g`**
    - The global textual input to be encoded. This text is processed by the CLIP model to extract relevant features and semantic information for the entire image.
    - Python dtype: `str`
    - Comfy dtype: `STRING`
- **`clip`**
    - Represents the CLIP model used for encoding the text. This model is a crucial component of the node, transforming textual data into a format that can be effectively utilized in image-text interaction tasks.
    - Python dtype: `object`
    - Comfy dtype: `CLIP`
- **`text_l`**
    - The local textual input to be encoded. This text provides additional details or focuses on specific areas within the image, complementing the global text input.
    - Python dtype: `str`
    - Comfy dtype: `STRING`
## Output types
- **`conditioning`**
    - The output is a conditioning format that combines the encoded text representation with additional information such as aesthetic score and image dimensions. This enriched representation is used to guide the generation or modification of images in a way that aligns with the encoded textual content.
    - Python dtype: `List[Tuple[torch.Tensor, Dict[str, Any]]]`
    - Comfy dtype: `CONDITIONING`
## Usage tips
- Infra type: `GPU`
- Common nodes: `KSampler,KSamplerAdvanced,ControlNetApplyAdvanced,Reroute,ToBasicPipe,KSampler (Efficient),Prompts Everywhere,ACN_AdvancedControlNetApply,FaceDetailer,KRestartSamplerAdv`


## Source code
```python
class CLIPTextEncodeSDXL:
    @classmethod
    def INPUT_TYPES(s):
        return {"required": {
            "width": ("INT", {"default": 1024.0, "min": 0, "max": MAX_RESOLUTION}),
            "height": ("INT", {"default": 1024.0, "min": 0, "max": MAX_RESOLUTION}),
            "crop_w": ("INT", {"default": 0, "min": 0, "max": MAX_RESOLUTION}),
            "crop_h": ("INT", {"default": 0, "min": 0, "max": MAX_RESOLUTION}),
            "target_width": ("INT", {"default": 1024.0, "min": 0, "max": MAX_RESOLUTION}),
            "target_height": ("INT", {"default": 1024.0, "min": 0, "max": MAX_RESOLUTION}),
            "text_g": ("STRING", {"multiline": True, "default": "CLIP_G"}), "clip": ("CLIP", ),
            "text_l": ("STRING", {"multiline": True, "default": "CLIP_L"}), "clip": ("CLIP", ),
            }}
    RETURN_TYPES = ("CONDITIONING",)
    FUNCTION = "encode"

    CATEGORY = "advanced/conditioning"

    def encode(self, clip, width, height, crop_w, crop_h, target_width, target_height, text_g, text_l):
        tokens = clip.tokenize(text_g)
        tokens["l"] = clip.tokenize(text_l)["l"]
        if len(tokens["l"]) != len(tokens["g"]):
            empty = clip.tokenize("")
            while len(tokens["l"]) < len(tokens["g"]):
                tokens["l"] += empty["l"]
            while len(tokens["l"]) > len(tokens["g"]):
                tokens["g"] += empty["g"]
        cond, pooled = clip.encode_from_tokens(tokens, return_pooled=True)
        return ([[cond, {"pooled_output": pooled, "width": width, "height": height, "crop_w": crop_w, "crop_h": crop_h, "target_width": target_width, "target_height": target_height}]], )

```
