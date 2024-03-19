# VAE Encode
## Documentation
- Class name: `VAEEncode`
- Category: `latent`
- Output node: `False`

This node encodes an image into a latent representation using a specified VAE model. The encoding process involves transforming the input image's pixel values to a latent space, which can be used for various generative tasks.
## Input types
### Required
- **`pixels`**
    - The input image to be encoded into a latent representation. This image is processed and its pixel values are transformed into a latent space by the VAE model.
    - Python dtype: `torch.Tensor`
    - Comfy dtype: `IMAGE`
- **`vae`**
    - The VAE model used for encoding the input image into a latent representation. This model is responsible for the transformation of pixel values into a latent space.
    - Python dtype: `comfy.sd.VAE`
    - Comfy dtype: `VAE`
## Output types
- **`latent`**
    - The encoded latent representation of the input image. This output can be used for various generative tasks that require a latent space representation.
    - Python dtype: `Dict[str, torch.Tensor]`
    - Comfy dtype: `LATENT`
## Usage tips
- Infra type: `GPU`
- Common nodes: `KSampler,KSamplerAdvanced,SetLatentNoiseMask,ImpactKSamplerBasicPipe,KSampler (Efficient),BNK_Unsampler,LatentUpscale,KSampler //Inspire,DZ_Face_Detailer,LatentUpscaleBy`


## Source code
```python
class VAEEncode:
    @classmethod
    def INPUT_TYPES(s):
        return {"required": { "pixels": ("IMAGE", ), "vae": ("VAE", )}}
    RETURN_TYPES = ("LATENT",)
    FUNCTION = "encode"

    CATEGORY = "latent"

    def encode(self, vae, pixels):
        t = vae.encode(pixels[:,:,:,:3])
        return ({"samples":t}, )

```
