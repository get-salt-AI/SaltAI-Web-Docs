# Inpainting VAE Encode (WAS)
## Documentation
- Class name: `VAEEncodeForInpaint (WAS)`
- Category: `latent/inpaint`
- Output node: `False`

This node is designed for encoding images for inpainting tasks using a Variational Autoencoder (VAE). It preprocesses the input image and mask to ensure compatibility with the VAE's requirements, applies mask modifications based on the provided offset, and then encodes the modified image into a latent representation suitable for inpainting.
## Input types
### Required
- **`pixels`**
    - The input image to be encoded for inpainting. It undergoes preprocessing to match the VAE's input dimensions and is modified based on the mask before encoding.
    - Python dtype: `torch.Tensor`
    - Comfy dtype: `IMAGE`
- **`vae`**
    - The Variational Autoencoder (VAE) model used for encoding the preprocessed image into a latent representation.
    - Python dtype: `torch.nn.Module`
    - Comfy dtype: `VAE`
- **`mask`**
    - A mask indicating the regions to inpaint. It is used to modify the input image before encoding.
    - Python dtype: `torch.Tensor`
    - Comfy dtype: `MASK`
- **`mask_offset`**
    - Determines how much to erode or dilate the mask. This affects the area of the image that will be inpainted.
    - Python dtype: `int`
    - Comfy dtype: `INT`
## Output types
- **`latent`**
    - The encoded latent representation of the input image, suitable for inpainting tasks.
    - Python dtype: `Dict[str, torch.Tensor]`
    - Comfy dtype: `LATENT`
## Usage tips
- Infra type: `GPU`
- Common nodes: unknown

The VAEEncodeForInpaint (WAS) node is designed for encoding images for inpainting tasks, where it preprocesses the input image and mask to match the VAE's requirements, modifies the mask based on the provided offset, and encodes the image into a latent representation suitable for inpainting. It is often used with inpainting models to prepare the image and mask for generating the inpainted output.
## Source code
```python
class WAS_VAEEncodeForInpaint:
    @classmethod
    def INPUT_TYPES(s):
        return {"required": { "pixels": ("IMAGE", ), "vae": ("VAE", ), "mask": ("MASK", ), "mask_offset": ("INT", {"default": 6, "min": -128, "max": 128, "step": 1}),}}
    RETURN_TYPES = ("LATENT",)
    FUNCTION = "encode"

    CATEGORY = "latent/inpaint"

    def encode(self, vae, pixels, mask, mask_offset=6):
        x = (pixels.shape[1] // 8) * 8
        y = (pixels.shape[2] // 8) * 8
        mask = torch.nn.functional.interpolate(mask.reshape((-1, 1, mask.shape[-2], mask.shape[-1])), size=(pixels.shape[1], pixels.shape[2]), mode="bilinear")

        pixels = pixels.clone()
        if pixels.shape[1] != x or pixels.shape[2] != y:
            x_offset = (pixels.shape[1] % 8) // 2
            y_offset = (pixels.shape[2] % 8) // 2
            pixels = pixels[:,x_offset:x + x_offset, y_offset:y + y_offset,:]
            mask = mask[:,:,x_offset:x + x_offset, y_offset:y + y_offset]

        mask_erosion = self.modify_mask(mask, mask_offset)

        m = (1.0 - mask_erosion.round()).squeeze(1)
        for i in range(3):
            pixels[:,:,:,i] -= 0.5
            pixels[:,:,:,i] *= m
            pixels[:,:,:,i] += 0.5
        t = vae.encode(pixels)

        return ({"samples":t, "noise_mask": (mask_erosion[:,:,:x,:y].round())}, )
        
    def modify_mask(self, mask, modify_by):
        if modify_by == 0:
            return mask
        if modify_by > 0:
            kernel_size = 2 * modify_by + 1
            kernel_tensor = torch.ones((1, 1, kernel_size, kernel_size))
            padding = modify_by
            modified_mask = torch.clamp(torch.nn.functional.conv2d(mask.round(), kernel_tensor, padding=padding), 0, 1)
        else:
            kernel_size = 2 * abs(modify_by) + 1
            kernel_tensor = torch.ones((1, 1, kernel_size, kernel_size))
            padding = abs(modify_by)
            eroded_mask = torch.nn.functional.conv2d(1 - mask.round(), kernel_tensor, padding=padding)
            modified_mask = torch.clamp(1 - eroded_mask, 0, 1)
        return modified_mask

```
