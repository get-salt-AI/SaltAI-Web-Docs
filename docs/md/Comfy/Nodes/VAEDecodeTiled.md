# VAE Decode (Tiled)
## Documentation
- Class name: `VAEDecodeTiled`
- Category: `_for_testing`
- Output node: `False`

The VAEDecodeTiled node is designed for decoding latent representations into images, specifically optimized for handling large images by processing them in tiles. This method allows for efficient memory usage and can accommodate images that exceed the size limitations of standard decoding techniques.
## Input types
### Required
- **`samples`**
    - Comfy dtype: `LATENT`
    - The 'samples' parameter represents the latent representations that are to be decoded into images. It is crucial for the decoding process as it contains the encoded information that will be transformed back into visual form.
    - Python dtype: `torch.Tensor`
- **`vae`**
    - Comfy dtype: `VAE`
    - The 'vae' parameter is the variational autoencoder model used for the decoding process. It plays a central role in transforming the latent representations into images.
    - Python dtype: `torch.nn.Module`
- **`tile_size`**
    - Comfy dtype: `INT`
    - The 'tile_size' parameter specifies the dimensions of the tiles used in the tiled decoding process. It affects the granularity of the decoding, with smaller tiles potentially allowing for finer control over memory usage and processing time.
    - Python dtype: `int`
## Output types
- **`image`**
    - Comfy dtype: `IMAGE`
    - The output is an image generated from the decoded latent representations, processed in a tiled manner to efficiently handle large image sizes.
    - Python dtype: `torch.Tensor`
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
