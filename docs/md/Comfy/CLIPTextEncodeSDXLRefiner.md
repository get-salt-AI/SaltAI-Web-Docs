# CLIPTextEncodeSDXLRefiner
## Documentation
- Class name: `CLIPTextEncodeSDXLRefiner`
- Category: `advanced/conditioning`
- Output node: `False`

This node refines the encoding of text inputs using a CLIP model, specifically designed for aesthetic scoring and image dimension adjustments. It tokenizes the input text, encodes it to generate conditioning information, and enriches this with aesthetic scores and image dimensions.
## Input types
### Required
- **`ascore`**
    - The aesthetic score to be associated with the encoded text, influencing the visual appeal of generated images.
    - Python dtype: `float`
    - Comfy dtype: `FLOAT`
- **`width`**
    - The desired width of the output image. This parameter helps in adjusting the dimensions of the generated image.
    - Python dtype: `int`
    - Comfy dtype: `INT`
- **`height`**
    - The desired height of the output image, used alongside width to define the image dimensions.
    - Python dtype: `int`
    - Comfy dtype: `INT`
- **`text`**
    - The text input to be encoded. This is the primary content that will be transformed into a conditioning vector.
    - Python dtype: `str`
    - Comfy dtype: `STRING`
- **`clip`**
    - The CLIP model used for text tokenization and encoding.
    - Python dtype: `torch.nn.Module`
    - Comfy dtype: `CLIP`
## Output types
- **`conditioning`**
    - The output conditioning vector, enriched with aesthetic scores and image dimensions, ready for further processing in image generation pipelines.
    - Python dtype: `List[Tuple[torch.Tensor, Dict[str, Union[float, int]]]]`
    - Comfy dtype: `CONDITIONING`
## Usage tips
- Infra type: `GPU`
- Common nodes: `KSamplerAdvanced,UltimateSDUpscale`


## Source code
```python
class CLIPTextEncodeSDXLRefiner:
    @classmethod
    def INPUT_TYPES(s):
        return {"required": {
            "ascore": ("FLOAT", {"default": 6.0, "min": 0.0, "max": 1000.0, "step": 0.01}),
            "width": ("INT", {"default": 1024.0, "min": 0, "max": MAX_RESOLUTION}),
            "height": ("INT", {"default": 1024.0, "min": 0, "max": MAX_RESOLUTION}),
            "text": ("STRING", {"multiline": True}), "clip": ("CLIP", ),
            }}
    RETURN_TYPES = ("CONDITIONING",)
    FUNCTION = "encode"

    CATEGORY = "advanced/conditioning"

    def encode(self, clip, ascore, width, height, text):
        tokens = clip.tokenize(text)
        cond, pooled = clip.encode_from_tokens(tokens, return_pooled=True)
        return ([[cond, {"pooled_output": pooled, "aesthetic_score": ascore, "width": width,"height": height}]], )

```
