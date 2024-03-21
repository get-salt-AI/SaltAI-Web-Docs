# StableCascade_StageC_VAEEncode
## Documentation
- Class name: `StableCascade_StageC_VAEEncode`
- Category: `_for_testing/stable_cascade`
- Output node: `False`

This node is responsible for encoding an image into a latent representation using a specified VAE model, with an adjustable compression factor. It performs an initial resizing of the input image based on the compression factor and the VAE's downscale ratio, then encodes the resized image into a latent space representation. Additionally, it generates a zero-initialized latent representation for stage B.
## Input types
### Required
- **`image`**
    - The input image to be encoded into a latent representation. It is crucial for the encoding process as it directly influences the generated latent representation.
    - Python dtype: `torch.Tensor`
    - Comfy dtype: `IMAGE`
- **`vae`**
    - The VAE model used for encoding the image. It determines the encoding mechanism and the characteristics of the generated latent representation.
    - Python dtype: `StageC_coder`
    - Comfy dtype: `VAE`
- **`compression`**
    - A factor that adjusts the size of the input image before encoding. It affects the resolution of the generated latent representation and allows for control over the level of detail.
    - Python dtype: `int`
    - Comfy dtype: `INT`
## Output types
- **`latent`**
    - A zero-initialized latent representation for use in stage B of the cascade process.
    - Python dtype: `torch.Tensor`
    - Comfy dtype: `LATENT`
## Usage tips
- Infra type: `GPU`
- Common nodes: unknown


## Source code
```python
class StableCascade_StageC_VAEEncode:
    def __init__(self, device="cpu"):
        self.device = device

    @classmethod
    def INPUT_TYPES(s):
        return {"required": {
            "image": ("IMAGE",),
            "vae": ("VAE", ),
            "compression": ("INT", {"default": 42, "min": 4, "max": 128, "step": 1}),
        }}
    RETURN_TYPES = ("LATENT", "LATENT")
    RETURN_NAMES = ("stage_c", "stage_b")
    FUNCTION = "generate"

    CATEGORY = "_for_testing/stable_cascade"

    def generate(self, image, vae, compression):
        width = image.shape[-2]
        height = image.shape[-3]
        out_width = (width // compression) * vae.downscale_ratio
        out_height = (height // compression) * vae.downscale_ratio

        s = comfy.utils.common_upscale(image.movedim(-1,1), out_width, out_height, "bicubic", "center").movedim(1,-1)

        c_latent = vae.encode(s[:,:,:,:3])
        b_latent = torch.zeros([c_latent.shape[0], 4, height // 4, width // 4])
        return ({
            "samples": c_latent,
        }, {
            "samples": b_latent,
        })

```
