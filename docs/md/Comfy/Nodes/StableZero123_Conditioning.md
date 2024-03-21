# StableZero123_Conditioning
## Documentation
- Class name: `StableZero123_Conditioning`
- Category: `conditioning/3d_models`
- Output node: `False`

This node is designed to process conditioning data for 3D models, specifically focusing on adjusting the conditioning based on the input parameters such as clip vision, initial image, and VAE model. It plays a crucial role in preparing the conditioning for further processing or generation tasks within the context of 3D model conditioning.
## Input types
### Required
- **`clip_vision`**
    - Specifies the CLIP model's vision to be used for conditioning, influencing the direction and focus of the generated content.
    - Python dtype: `torch.Tensor`
    - Comfy dtype: `CLIP_VISION`
- **`init_image`**
    - The initial image to start the conditioning process, serving as a base or reference for further modifications.
    - Python dtype: `torch.Tensor`
    - Comfy dtype: `IMAGE`
- **`vae`**
    - The VAE model used for encoding or decoding images, integral to the conditioning process for 3D models.
    - Python dtype: `torch.nn.Module`
    - Comfy dtype: `VAE`
- **`width`**
    - Defines the width of the output image, affecting the dimensionality of the conditioned output.
    - Python dtype: `int`
    - Comfy dtype: `INT`
- **`height`**
    - Specifies the height of the output image, impacting the dimensionality of the conditioned output.
    - Python dtype: `int`
    - Comfy dtype: `INT`
- **`batch_size`**
    - Determines the number of conditioning operations to be processed in a single batch, influencing the efficiency and speed of the node's execution.
    - Python dtype: `int`
    - Comfy dtype: `INT`
- **`elevation`**
    - Sets the elevation angle for the 3D model conditioning, affecting the perspective and orientation of the generated content.
    - Python dtype: `float`
    - Comfy dtype: `FLOAT`
- **`azimuth`**
    - Defines the azimuth angle for the 3D model conditioning, altering the direction and viewpoint of the generated content.
    - Python dtype: `float`
    - Comfy dtype: `FLOAT`
## Output types
- **`conditioning`**
    - The negative conditioning output, designed for suppressing certain features or aspects in the generated content.
    - Python dtype: `List[Tuple[torch.Tensor, Dict[str, Any]]]`
    - Comfy dtype: `CONDITIONING`
- **`latent`**
    - The latent representation derived from the conditioning process, essential for further processing or generation tasks.
    - Python dtype: `List[Tuple[torch.Tensor, Dict[str, Any]]]`
    - Comfy dtype: `LATENT`
## Usage tips
- Infra type: `GPU`
- Common nodes: `KSampler,SamplerCustom`


## Source code
```python
class StableZero123_Conditioning:
    @classmethod
    def INPUT_TYPES(s):
        return {"required": { "clip_vision": ("CLIP_VISION",),
                              "init_image": ("IMAGE",),
                              "vae": ("VAE",),
                              "width": ("INT", {"default": 256, "min": 16, "max": nodes.MAX_RESOLUTION, "step": 8}),
                              "height": ("INT", {"default": 256, "min": 16, "max": nodes.MAX_RESOLUTION, "step": 8}),
                              "batch_size": ("INT", {"default": 1, "min": 1, "max": 4096}),
                              "elevation": ("FLOAT", {"default": 0.0, "min": -180.0, "max": 180.0}),
                              "azimuth": ("FLOAT", {"default": 0.0, "min": -180.0, "max": 180.0}),
                             }}
    RETURN_TYPES = ("CONDITIONING", "CONDITIONING", "LATENT")
    RETURN_NAMES = ("positive", "negative", "latent")

    FUNCTION = "encode"

    CATEGORY = "conditioning/3d_models"

    def encode(self, clip_vision, init_image, vae, width, height, batch_size, elevation, azimuth):
        output = clip_vision.encode_image(init_image)
        pooled = output.image_embeds.unsqueeze(0)
        pixels = comfy.utils.common_upscale(init_image.movedim(-1,1), width, height, "bilinear", "center").movedim(1,-1)
        encode_pixels = pixels[:,:,:,:3]
        t = vae.encode(encode_pixels)
        cam_embeds = camera_embeddings(elevation, azimuth)
        cond = torch.cat([pooled, cam_embeds.to(pooled.device).repeat((pooled.shape[0], 1, 1))], dim=-1)

        positive = [[cond, {"concat_latent_image": t}]]
        negative = [[torch.zeros_like(pooled), {"concat_latent_image": torch.zeros_like(t)}]]
        latent = torch.zeros([batch_size, 4, height // 8, width // 8])
        return (positive, negative, {"samples":latent})

```
