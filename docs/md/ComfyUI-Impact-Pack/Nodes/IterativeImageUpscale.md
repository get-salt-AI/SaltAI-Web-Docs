# Iterative Upscale (Image)
## Documentation
- Class name: `IterativeImageUpscale`
- Category: `ImpactPack/Upscale`
- Output node: `False`

The `IterativeImageUpscale` node is designed to upscale images in a stepwise manner. It takes an image and applies a series of upscaling operations, each increasing the image's resolution or quality incrementally. This iterative approach allows for more controlled upscaling, potentially leading to higher quality results with fewer artifacts.
## Input types
### Required
- **`pixels`**
    - The input image to be upscaled. This image serves as the base for the iterative upscaling process.
    - Python dtype: `torch.Tensor`
    - Comfy dtype: `IMAGE`
- **`upscale_factor`**
    - The total factor by which the image's resolution is to be increased. This factor guides the iterative upscaling process.
    - Python dtype: `float`
    - Comfy dtype: `FLOAT`
- **`steps`**
    - The number of iterative steps to perform during the upscaling. Each step incrementally increases the image's resolution.
    - Python dtype: `int`
    - Comfy dtype: `INT`
- **`temp_prefix`**
    - A temporary prefix used for intermediate processing steps. It can be an empty string.
    - Python dtype: `str`
    - Comfy dtype: `STRING`
- **`upscaler`**
    - The upscaling model or method to be applied at each iterative step.
    - Python dtype: `object`
    - Comfy dtype: `UPSCALER`
- **`vae`**
    - unknown
    - Python dtype: `unknown`
    - Comfy dtype: `VAE`
## Output types
- **`image`**
    - The upscaled image after all iterative steps have been completed.
    - Python dtype: `torch.Tensor`
    - Comfy dtype: `IMAGE`
## Usage tips
- Infra type: `GPU`
- Common nodes: `PreviewImage,ImageBatch`


## Source code
```python
class IterativeImageUpscale:
    @classmethod
    def INPUT_TYPES(s):
        return {"required": {
                     "pixels": ("IMAGE", ),
                     "upscale_factor": ("FLOAT", {"default": 1.5, "min": 1, "max": 10000, "step": 0.1}),
                     "steps": ("INT", {"default": 3, "min": 1, "max": 10000, "step": 1}),
                     "temp_prefix": ("STRING", {"default": ""}),
                     "upscaler": ("UPSCALER",),
                     "vae": ("VAE",),
                },
                "hidden": {"unique_id": "UNIQUE_ID"}
        }

    RETURN_TYPES = ("IMAGE",)
    RETURN_NAMES = ("image",)
    FUNCTION = "doit"

    CATEGORY = "ImpactPack/Upscale"

    def doit(self, pixels, upscale_factor, steps, temp_prefix, upscaler, vae, unique_id):
        if temp_prefix == "":
            temp_prefix = None

        core.update_node_status(unique_id, "VAEEncode (first)", 0)
        if upscaler.is_tiled:
            latent = nodes.VAEEncodeTiled().encode(vae, pixels, upscaler.tile_size)[0]
        else:
            latent = nodes.VAEEncode().encode(vae, pixels)[0]

        refined_latent = IterativeLatentUpscale().doit(latent, upscale_factor, steps, temp_prefix, upscaler, unique_id)

        core.update_node_status(unique_id, "VAEDecode (final)", 1.0)
        if upscaler.is_tiled:
            pixels = nodes.VAEDecodeTiled().decode(vae, refined_latent[0], upscaler.tile_size)[0]
        else:
            pixels = nodes.VAEDecode().decode(vae, refined_latent[0])[0]

        core.update_node_status(unique_id, "", None)

        return (pixels, )

```
