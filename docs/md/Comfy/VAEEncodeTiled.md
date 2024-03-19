# VAE Encode (Tiled)
## Documentation
- Class name: `VAEEncodeTiled`
- Category: `_for_testing`
- Output node: `False`

This node performs tiled encoding of an image using a Variational Autoencoder (VAE). It divides the input image into tiles of a specified size and encodes each tile separately. This approach is useful for handling large images that may not fit into memory if processed as a whole.
## Input types
### Required
- **`pixels`**
    - The input image to be encoded. The tiling process allows for efficient handling of large images by breaking them down into smaller, manageable pieces.
    - Python dtype: `torch.Tensor`
    - Comfy dtype: `IMAGE`
- **`vae`**
    - The Variational Autoencoder model used for encoding the image. This model is responsible for transforming the input image into a latent representation.
    - Python dtype: `comfy.sd.VAE`
    - Comfy dtype: `VAE`
- **`tile_size`**
    - The size of the tiles into which the input image is divided. This parameter allows for flexibility in managing the trade-off between memory usage and granularity of encoding.
    - Python dtype: `int`
    - Comfy dtype: `INT`
## Output types
- **`latent`**
    - The encoded representation of the input image in the latent space. This output captures the essential features of the image in a compressed form.
    - Python dtype: `Dict[str, torch.Tensor]`
    - Comfy dtype: `LATENT`
## Usage tips
- Infra type: `GPU`
- Common nodes: `KSampler,CR Interpolate Latents,KSampler //Inspire,BNK_TiledKSampler,KSampler (Efficient)`


## Source code
```python
class VAEEncodeTiled:
    @classmethod
    def INPUT_TYPES(s):
        return {"required": {"pixels": ("IMAGE", ), "vae": ("VAE", ),
                             "tile_size": ("INT", {"default": 512, "min": 320, "max": 4096, "step": 64})
                            }}
    RETURN_TYPES = ("LATENT",)
    FUNCTION = "encode"

    CATEGORY = "_for_testing"

    def encode(self, vae, pixels, tile_size):
        t = vae.encode_tiled(pixels[:,:,:,:3], tile_x=tile_size, tile_y=tile_size, )
        return ({"samples":t}, )

```
