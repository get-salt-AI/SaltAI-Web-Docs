# CLIP Vision Encode
## Documentation
- Class name: `CLIPVisionEncode`
- Category: `conditioning`
- Output node: `False`

The `CLIPVisionEncode` node is responsible for encoding an image using a CLIP vision model. It takes an image as input and processes it through the CLIP vision model's `encode_image` method to produce an output that represents the encoded features of the image.
## Input types
### Required
- **`clip_vision`**
    - The CLIP vision model used for encoding the image. It is crucial for transforming the raw image data into a format that can be effectively utilized for further processing or analysis.
    - Python dtype: `CLIPVision`
    - Comfy dtype: `CLIP_VISION`
- **`image`**
    - The image to be encoded. This input is transformed by the CLIP vision model into an encoded representation, capturing essential features and characteristics of the image.
    - Python dtype: `torch.Tensor`
    - Comfy dtype: `IMAGE`
## Output types
- **`clip_vision_output`**
    - The encoded representation of the image, as produced by the CLIP vision model. This output captures the essential features and characteristics of the image, suitable for further processing or analysis.
    - Python dtype: `CLIPVisionOutput`
    - Comfy dtype: `CLIP_VISION_OUTPUT`
## Usage tips
- Infra type: `GPU`
- Common nodes: `unCLIPConditioning`


## Source code
```python
class CLIPVisionEncode:
    @classmethod
    def INPUT_TYPES(s):
        return {"required": { "clip_vision": ("CLIP_VISION",),
                              "image": ("IMAGE",)
                             }}
    RETURN_TYPES = ("CLIP_VISION_OUTPUT",)
    FUNCTION = "encode"

    CATEGORY = "conditioning"

    def encode(self, clip_vision, image):
        output = clip_vision.encode_image(image)
        return (output,)

```
