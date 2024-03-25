# VAE Decode
## Documentation
- Class name: `VAEDecode`
- Category: `latent`
- Output node: `False`

The VAEDecode node is designed for decoding latent representations into images using a specified Variational Autoencoder (VAE). It serves the purpose of generating images from compressed data representations, facilitating the reconstruction of images from their latent space encodings.
## Input types
### Required
- **`samples`**
    - Comfy dtype: `LATENT`
    - The 'samples' parameter represents the latent representations to be decoded into images. It is crucial for the decoding process as it provides the compressed data from which the images are reconstructed.
    - Python dtype: `torch.Tensor`
- **`vae`**
    - Comfy dtype: `VAE`
    - The 'vae' parameter specifies the Variational Autoencoder model to be used for decoding the latent representations into images. It is essential for determining the decoding mechanism and the quality of the reconstructed images.
    - Python dtype: `torch.nn.Module`
## Output types
- **`image`**
    - Comfy dtype: `IMAGE`
    - The output is an image reconstructed from the provided latent representation using the specified VAE model.
    - Python dtype: `torch.Tensor`
## Usage tips
- Infra type: `GPU`
- Common nodes: `PreviewImage,SaveImage,VHS_VideoCombine,Reroute,ImageUpscaleWithModel,UltimateSDUpscale,RIFE VFI,FaceDetailer,FILM VFI,ColorMatch`


## Source code
```python
class VAEDecode:
    @classmethod
    def INPUT_TYPES(s):
        return {"required": { "samples": ("LATENT", ), "vae": ("VAE", )}}
    RETURN_TYPES = ("IMAGE",)
    FUNCTION = "decode"

    CATEGORY = "latent"

    def decode(self, vae, samples):
        return (vae.decode(samples["samples"]), )

```
