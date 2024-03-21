# SD_4XUpscale_Conditioning
## Documentation
- Class name: `SD_4XUpscale_Conditioning`
- Category: `conditioning/upscale_diffusion`
- Output node: `False`

This node is designed for conditioning and upscaling images by a factor of 4x using diffusion models. It takes images along with positive and negative conditioning phrases, applies noise augmentation, and scales the images, returning enhanced conditioning and a latent representation.
## Input types
### Required
- **`images`**
    - The input images to be upscaled. These images are the primary subject for the upscaling process.
    - Python dtype: `torch.Tensor`
    - Comfy dtype: `IMAGE`
- **`positive`**
    - Positive conditioning phrases that guide the upscaling process towards desired attributes or features in the output images.
    - Python dtype: `List[str]`
    - Comfy dtype: `CONDITIONING`
- **`negative`**
    - Negative conditioning phrases used to steer away the upscaling process from undesired attributes or features.
    - Python dtype: `List[str]`
    - Comfy dtype: `CONDITIONING`
- **`scale_ratio`**
    - Determines the scaling factor for the upscaling process. A higher value results in a larger output image.
    - Python dtype: `float`
    - Comfy dtype: `FLOAT`
- **`noise_augmentation`**
    - Controls the level of noise augmentation applied to the images during the upscaling process, affecting the final image quality.
    - Python dtype: `float`
    - Comfy dtype: `FLOAT`
## Output types
- **`conditioning`**
    - Enhanced negative conditioning phrases after the upscaling process.
    - Python dtype: `List[str]`
    - Comfy dtype: `CONDITIONING`
- **`latent`**
    - A latent representation of the upscaled images, useful for further processing or analysis.
    - Python dtype: `torch.Tensor`
    - Comfy dtype: `LATENT`
## Usage tips
- Infra type: `GPU`
- Common nodes: `KSampler`


## Source code
```python
class SD_4XUpscale_Conditioning:
    @classmethod
    def INPUT_TYPES(s):
        return {"required": { "images": ("IMAGE",),
                              "positive": ("CONDITIONING",),
                              "negative": ("CONDITIONING",),
                              "scale_ratio": ("FLOAT", {"default": 4.0, "min": 0.0, "max": 10.0, "step": 0.01}),
                              "noise_augmentation": ("FLOAT", {"default": 0.0, "min": 0.0, "max": 1.0, "step": 0.001}),
                             }}
    RETURN_TYPES = ("CONDITIONING", "CONDITIONING", "LATENT")
    RETURN_NAMES = ("positive", "negative", "latent")

    FUNCTION = "encode"

    CATEGORY = "conditioning/upscale_diffusion"

    def encode(self, images, positive, negative, scale_ratio, noise_augmentation):
        width = max(1, round(images.shape[-2] * scale_ratio))
        height = max(1, round(images.shape[-3] * scale_ratio))

        pixels = comfy.utils.common_upscale((images.movedim(-1,1) * 2.0) - 1.0, width // 4, height // 4, "bilinear", "center")

        out_cp = []
        out_cn = []

        for t in positive:
            n = [t[0], t[1].copy()]
            n[1]['concat_image'] = pixels
            n[1]['noise_augmentation'] = noise_augmentation
            out_cp.append(n)

        for t in negative:
            n = [t[0], t[1].copy()]
            n[1]['concat_image'] = pixels
            n[1]['noise_augmentation'] = noise_augmentation
            out_cn.append(n)

        latent = torch.zeros([images.shape[0], 4, height // 4, width // 4])
        return (out_cp, out_cn, {"samples":latent})

```
