# VAE Decode (Tiled)
## Documentation
- Class name: `VAEDecodeTiled`
- Category: `_for_testing`
- Output node: `False`

The VAEDecodeTiled node is designed for decoding latent representations into images using a tiled approach. This method is particularly useful for handling large images by breaking them down into smaller tiles, decoding each tile separately, and then stitching them back together. This approach can help manage memory usage and computational load more efficiently.
## Input types
### Required
- **`samples`**
    - The latent representation of an image or a set of images that the VAE model will decode. This input is crucial for generating the final image output.
    - Python dtype: `torch.Tensor`
    - Comfy dtype: `LATENT`
- **`vae`**
    - The Variational Autoencoder (VAE) model used for decoding the latent representations into images. The choice of VAE model can significantly affect the quality and characteristics of the generated images.
    - Python dtype: `comfy.sd.VAE`
    - Comfy dtype: `VAE`
- **`tile_size`**
    - Specifies the size of the tiles used in the decoding process. Adjusting the tile size can influence the balance between computational efficiency and the quality of the generated images.
    - Python dtype: `int`
    - Comfy dtype: `INT`
## Output types
- **`image`**
    - The decoded image or set of images generated from the latent representations. This output is the direct result of the tiled decoding process.
    - Python dtype: `torch.Tensor`
    - Comfy dtype: `IMAGE`
## Usage tips
- Infra type: `GPU`
- Common nodes: `SaveImage,ImageListToImageBatch,ImageUpscaleWithModel,Anything Everywhere,AlphaChanelAdd,GetImageSize,VHS_VideoCombine,ImageSender,ControlNetApplyAdvanced,PreviewImage`


## Source code
```python
class VAEDecodeTiled:
    @classmethod
    def INPUT_TYPES(s):
        return {"required": {"samples": ("LATENT", ), "vae": ("VAE", ),
                             "tile_size": ("INT", {"default": 512, "min": 320, "max": 4096, "step": 64})
                            }}
    RETURN_TYPES = ("IMAGE",)
    FUNCTION = "decode"

    CATEGORY = "_for_testing"

    def decode(self, vae, samples, tile_size):
        return (vae.decode_tiled(samples["samples"], tile_x=tile_size // 8, tile_y=tile_size // 8, ), )

```
