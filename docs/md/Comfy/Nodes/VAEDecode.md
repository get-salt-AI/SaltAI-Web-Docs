# VAE Decode
## Documentation
- Class name: `VAEDecode`
- Category: `latent`
- Output node: `False`

This node decodes latent representations into images using a specified VAE model. It takes latent samples as input and utilizes the VAE's decode method to transform these samples back into image space.
## Input types
### Required
- **`samples`**
    - Latent representations to be decoded into images. These are essential for generating the final output images from the compressed latent space.
    - Python dtype: `torch.Tensor`
    - Comfy dtype: `LATENT`
- **`vae`**
    - The VAE model used for decoding the latent samples into images. It defines the specific architecture and parameters for the decoding process.
    - Python dtype: `torch.nn.Module`
    - Comfy dtype: `VAE`
## Output types
- **`image`**
    - The decoded images generated from the latent samples using the VAE model.
    - Python dtype: `torch.Tensor`
    - Comfy dtype: `IMAGE`
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
