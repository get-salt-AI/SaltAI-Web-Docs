# InpaintModelConditioning
## Documentation
- Class name: `InpaintModelConditioning`
- Category: `conditioning/inpaint`
- Output node: `False`

The InpaintModelConditioning node is designed for conditioning the inpainting process by encoding the input parameters into a format suitable for the model. It takes in positive and negative conditioning, a VAE model, an image, and a mask to produce conditioning outputs and a latent representation.
## Input types
### Required
- **`positive`**
    - Positive conditioning influences the inpainting process by specifying desired features or attributes in the generated output.
    - Python dtype: `List[Tuple[torch.Tensor, Dict[str, Any]]]`
    - Comfy dtype: `CONDITIONING`
- **`negative`**
    - Negative conditioning specifies undesired features or attributes, helping to guide the model away from certain outcomes.
    - Python dtype: `List[Tuple[torch.Tensor, Dict[str, Any]]]`
    - Comfy dtype: `CONDITIONING`
- **`vae`**
    - The VAE model parameter is crucial for encoding the image into a latent space, facilitating the inpainting process.
    - Python dtype: `torch.nn.Module`
    - Comfy dtype: `VAE`
- **`pixels`**
    - The image to be inpainted, provided as pixel data.
    - Python dtype: `torch.Tensor`
    - Comfy dtype: `IMAGE`
- **`mask`**
    - A mask indicating the areas to inpaint, with the same dimensions as the input image.
    - Python dtype: `torch.Tensor`
    - Comfy dtype: `MASK`
## Output types
- **`conditioning`**
    - The modified negative conditioning after processing.
    - Python dtype: `List[Tuple[torch.Tensor, Dict[str, Any]]]`
    - Comfy dtype: `CONDITIONING`
- **`latent`**
    - A latent representation of the inpainted image.
    - Python dtype: `torch.Tensor`
    - Comfy dtype: `LATENT`
## Usage tips
- Infra type: `GPU`
- Common nodes: unknown


## Source code
```python
class InpaintModelConditioning:
    @classmethod
    def INPUT_TYPES(s):
        return {"required": {"positive": ("CONDITIONING", ),
                             "negative": ("CONDITIONING", ),
                             "vae": ("VAE", ),
                             "pixels": ("IMAGE", ),
                             "mask": ("MASK", ),
                             }}

    RETURN_TYPES = ("CONDITIONING","CONDITIONING","LATENT")
    RETURN_NAMES = ("positive", "negative", "latent")
    FUNCTION = "encode"

    CATEGORY = "conditioning/inpaint"

    def encode(self, positive, negative, pixels, vae, mask):
        x = (pixels.shape[1] // 8) * 8
        y = (pixels.shape[2] // 8) * 8
        mask = torch.nn.functional.interpolate(mask.reshape((-1, 1, mask.shape[-2], mask.shape[-1])), size=(pixels.shape[1], pixels.shape[2]), mode="bilinear")

        orig_pixels = pixels
        pixels = orig_pixels.clone()
        if pixels.shape[1] != x or pixels.shape[2] != y:
            x_offset = (pixels.shape[1] % 8) // 2
            y_offset = (pixels.shape[2] % 8) // 2
            pixels = pixels[:,x_offset:x + x_offset, y_offset:y + y_offset,:]
            mask = mask[:,:,x_offset:x + x_offset, y_offset:y + y_offset]

        m = (1.0 - mask.round()).squeeze(1)
        for i in range(3):
            pixels[:,:,:,i] -= 0.5
            pixels[:,:,:,i] *= m
            pixels[:,:,:,i] += 0.5
        concat_latent = vae.encode(pixels)
        orig_latent = vae.encode(orig_pixels)

        out_latent = {}

        out_latent["samples"] = orig_latent
        out_latent["noise_mask"] = mask

        out = []
        for conditioning in [positive, negative]:
            c = []
            for t in conditioning:
                d = t[1].copy()
                d["concat_latent_image"] = concat_latent
                d["concat_mask"] = mask
                n = [t[0], d]
                c.append(n)
            out.append(c)
        return (out[0], out[1], out_latent)

```
