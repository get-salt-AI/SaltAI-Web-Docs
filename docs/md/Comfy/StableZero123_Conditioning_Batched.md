# StableZero123_Conditioning_Batched
## Documentation
- Class name: `StableZero123_Conditioning_Batched`
- Category: `conditioning/3d_models`
- Output node: `False`

The functionality of the 'StableZero123_Conditioning_Batched' node cannot be directly inferred from the provided context, as specific details about its methods and operations are not given. However, based on the naming convention and the context of other nodes, it might be related to processing or modifying conditioning data in batches for stable generative models.
## Input types
### Required
- **`clip_vision`**
    - unknown
    - Python dtype: `unknown`
    - Comfy dtype: `CLIP_VISION`
- **`init_image`**
    - unknown
    - Python dtype: `unknown`
    - Comfy dtype: `IMAGE`
- **`vae`**
    - unknown
    - Python dtype: `unknown`
    - Comfy dtype: `VAE`
- **`width`**
    - unknown
    - Python dtype: `unknown`
    - Comfy dtype: `INT`
- **`height`**
    - unknown
    - Python dtype: `unknown`
    - Comfy dtype: `INT`
- **`batch_size`**
    - unknown
    - Python dtype: `unknown`
    - Comfy dtype: `INT`
- **`elevation`**
    - unknown
    - Python dtype: `unknown`
    - Comfy dtype: `FLOAT`
- **`azimuth`**
    - unknown
    - Python dtype: `unknown`
    - Comfy dtype: `FLOAT`
- **`elevation_batch_increment`**
    - unknown
    - Python dtype: `unknown`
    - Comfy dtype: `FLOAT`
- **`azimuth_batch_increment`**
    - unknown
    - Python dtype: `unknown`
    - Comfy dtype: `FLOAT`
## Output types
- **`conditioning`**
    - unknown
    - Python dtype: `unknown`
    - Comfy dtype: `CONDITIONING`
- **`latent`**
    - unknown
    - Python dtype: `unknown`
    - Comfy dtype: `LATENT`
## Usage tips
- Infra type: `CPU`
- Common nodes: unknown


## Source code
```python
class StableZero123_Conditioning_Batched:
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
                              "elevation_batch_increment": ("FLOAT", {"default": 0.0, "min": -180.0, "max": 180.0}),
                              "azimuth_batch_increment": ("FLOAT", {"default": 0.0, "min": -180.0, "max": 180.0}),
                             }}
    RETURN_TYPES = ("CONDITIONING", "CONDITIONING", "LATENT")
    RETURN_NAMES = ("positive", "negative", "latent")

    FUNCTION = "encode"

    CATEGORY = "conditioning/3d_models"

    def encode(self, clip_vision, init_image, vae, width, height, batch_size, elevation, azimuth, elevation_batch_increment, azimuth_batch_increment):
        output = clip_vision.encode_image(init_image)
        pooled = output.image_embeds.unsqueeze(0)
        pixels = comfy.utils.common_upscale(init_image.movedim(-1,1), width, height, "bilinear", "center").movedim(1,-1)
        encode_pixels = pixels[:,:,:,:3]
        t = vae.encode(encode_pixels)

        cam_embeds = []
        for i in range(batch_size):
            cam_embeds.append(camera_embeddings(elevation, azimuth))
            elevation += elevation_batch_increment
            azimuth += azimuth_batch_increment

        cam_embeds = torch.cat(cam_embeds, dim=0)
        cond = torch.cat([comfy.utils.repeat_to_batch_size(pooled, batch_size), cam_embeds], dim=-1)

        positive = [[cond, {"concat_latent_image": t}]]
        negative = [[torch.zeros_like(pooled), {"concat_latent_image": torch.zeros_like(t)}]]
        latent = torch.zeros([batch_size, 4, height // 8, width // 8])
        return (positive, negative, {"samples":latent, "batch_index": [0] * batch_size})

```
