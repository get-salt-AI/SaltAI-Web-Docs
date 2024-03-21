# VAE Encode Batched 🎥🅥🅗🅢
## Documentation
- Class name: `VHS_VAEEncodeBatched`
- Category: `Video Helper Suite 🎥🅥🅗🅢/batched nodes`
- Output node: `False`

The `VHS_VAEEncodeBatched` node is designed for batch processing of images through a Variational Autoencoder (VAE) to produce latent representations. It processes images in smaller batches to manage memory usage effectively, especially useful for handling large datasets or high-resolution images.
## Input types
### Required
- **`pixels`**
    - The `pixels` parameter represents the images to be encoded into latent space. It's crucial for defining the input data that will be transformed by the VAE.
    - Python dtype: `torch.Tensor`
    - Comfy dtype: `IMAGE`
- **`vae`**
    - The `vae` parameter specifies the Variational Autoencoder model used for encoding the images into latent representations. It's essential for determining the transformation applied to the input images.
    - Python dtype: `torch.nn.Module`
    - Comfy dtype: `VAE`
- **`per_batch`**
    - The `per_batch` parameter controls the number of images processed in each batch. This helps in managing memory usage and computational load during the encoding process.
    - Python dtype: `int`
    - Comfy dtype: `INT`
## Output types
- **`latent`**
    - The output `samples` are the encoded latent representations of the input images. These latent vectors are useful for various applications, including image generation and manipulation.
    - Python dtype: `Dict[str, torch.Tensor]`
    - Comfy dtype: `LATENT`
## Usage tips
- Infra type: `GPU`
- Common nodes: unknown


## Source code
```python
class VAEEncodeBatched:
    @classmethod
    def INPUT_TYPES(s):
        return {
            "required": {
                "pixels": ("IMAGE", ), "vae": ("VAE", ),
                "per_batch": ("INT", {"default": 16, "min": 1})
                }
            }
    
    CATEGORY = "Video Helper Suite 🎥🅥🅗🅢/batched nodes"

    RETURN_TYPES = ("LATENT",)
    FUNCTION = "encode"

    def encode(self, vae, pixels, per_batch):
        t = []
        for start_idx in range(0, pixels.shape[0], per_batch):
            sub_pixels = VAEEncode.vae_encode_crop_pixels(pixels[start_idx:start_idx+per_batch])
            t.append(vae.encode(sub_pixels[:,:,:,:3]))
        return ({"samples": torch.cat(t, dim=0)}, )

```
