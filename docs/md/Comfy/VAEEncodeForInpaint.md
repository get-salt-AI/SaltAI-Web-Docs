# VAE Encode (for Inpainting)
## Documentation
- Class name: `VAEEncodeForInpaint`
- Category: `latent/inpaint`
- Output node: `False`

This node is designed for encoding images for inpainting tasks using a Variational Autoencoder (VAE). Inpainting involves filling in missing or corrupted parts of images. The node processes images, applies a mask to identify the areas to inpaint, optionally grows the mask to ensure seamless inpainting, and encodes the modified image into a latent representation using a VAE.
## Input types
### Required
- **`pixels`**
    - The input image to be encoded for inpainting. It is the primary data that undergoes the inpainting process.
    - Python dtype: `torch.Tensor`
    - Comfy dtype: `IMAGE`
- **`vae`**
    - The Variational Autoencoder model used for encoding the image into a latent representation.
    - Python dtype: `VAE`
    - Comfy dtype: `VAE`
- **`mask`**
    - A mask indicating the areas of the image to be inpainted. The mask guides the inpainting process by highlighting the regions that need reconstruction.
    - Python dtype: `torch.Tensor`
    - Comfy dtype: `MASK`
- **`grow_mask_by`**
    - An optional parameter to expand the mask by a certain number of pixels. Growing the mask can help achieve smoother transitions between inpainted areas and the original image.
    - Python dtype: `int`
    - Comfy dtype: `INT`
## Output types
- **`latent`**
    - The encoded latent representation of the inpainted image. This output is used for further processing or generation tasks.
    - Python dtype: `Dict[str, torch.Tensor]`
    - Comfy dtype: `LATENT`
## Usage tips
- Infra type: `GPU`
- Common nodes: `VHS_DuplicateLatents,KSampler,Reroute,KSampler Adv. (Efficient),Fast Muter (rgthree),KSampler (Efficient),VAEDecode`


## Source code
```python
class VAEEncodeForInpaint:
    @classmethod
    def INPUT_TYPES(s):
        return {"required": { "pixels": ("IMAGE", ), "vae": ("VAE", ), "mask": ("MASK", ), "grow_mask_by": ("INT", {"default": 6, "min": 0, "max": 64, "step": 1}),}}
    RETURN_TYPES = ("LATENT",)
    FUNCTION = "encode"

    CATEGORY = "latent/inpaint"

    def encode(self, vae, pixels, mask, grow_mask_by=6):
        x = (pixels.shape[1] // vae.downscale_ratio) * vae.downscale_ratio
        y = (pixels.shape[2] // vae.downscale_ratio) * vae.downscale_ratio
        mask = torch.nn.functional.interpolate(mask.reshape((-1, 1, mask.shape[-2], mask.shape[-1])), size=(pixels.shape[1], pixels.shape[2]), mode="bilinear")

        pixels = pixels.clone()
        if pixels.shape[1] != x or pixels.shape[2] != y:
            x_offset = (pixels.shape[1] % vae.downscale_ratio) // 2
            y_offset = (pixels.shape[2] % vae.downscale_ratio) // 2
            pixels = pixels[:,x_offset:x + x_offset, y_offset:y + y_offset,:]
            mask = mask[:,:,x_offset:x + x_offset, y_offset:y + y_offset]

        #grow mask by a few pixels to keep things seamless in latent space
        if grow_mask_by == 0:
            mask_erosion = mask
        else:
            kernel_tensor = torch.ones((1, 1, grow_mask_by, grow_mask_by))
            padding = math.ceil((grow_mask_by - 1) / 2)

            mask_erosion = torch.clamp(torch.nn.functional.conv2d(mask.round(), kernel_tensor, padding=padding), 0, 1)

        m = (1.0 - mask.round()).squeeze(1)
        for i in range(3):
            pixels[:,:,:,i] -= 0.5
            pixels[:,:,:,i] *= m
            pixels[:,:,:,i] += 0.5
        t = vae.encode(pixels)

        return ({"samples":t, "noise_mask": (mask_erosion[:,:,:x,:y].round())}, )

```
