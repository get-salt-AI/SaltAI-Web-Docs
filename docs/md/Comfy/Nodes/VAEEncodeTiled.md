# VAE Encode (Tiled)
## Documentation
- Class name: `VAEEncodeTiled`
- Category: `_for_testing`
- Output node: `False`

The VAEEncodeTiled node is designed for encoding images into a latent space representation, specifically handling images in a tiled manner. This approach allows for processing larger images by dividing them into smaller, manageable tiles, encoding each separately, and then combining the results.
## Input types
### Required
- **`pixels`**
    - Comfy dtype: `IMAGE`
    - The 'pixels' parameter represents the image data to be encoded. It is crucial for defining the visual content that will be transformed into a latent representation.
    - Python dtype: `torch.Tensor`
- **`vae`**
    - Comfy dtype: `VAE`
    - The 'vae' parameter specifies the Variational Autoencoder model used for the encoding process. It plays a key role in determining how the image data is transformed into the latent space.
    - Python dtype: `comfy.sd.VAE`
- **`tile_size`**
    - Comfy dtype: `INT`
    - The 'tile_size' parameter determines the dimensions of the tiles into which the image is divided for encoding. It affects the granularity of the tiling process and can impact the encoding performance and quality.
    - Python dtype: `int`
## Output types
- **`latent`**
    - Comfy dtype: `LATENT`
    - The output is a latent space representation of the input image, encoded in a tiled manner. It captures the essential features of the image in a compressed form.
    - Python dtype: `Dict[str, torch.Tensor]`
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
