# Reencode Latent
## Documentation
- Class name: `ReencodeLatent`
- Category: `ImpactPack/Util`
- Output node: `False`

The `ReencodeLatent` node is designed for re-encoding latent representations of images. It allows for the transformation of latent samples through a specified input VAE model and then re-encodes them using an output VAE model. This process can be modified by a tile mode, which dictates whether the decoding and encoding steps should be performed with tiling to handle larger images or specific parts of the process.
## Input types
### Required
- **`samples`**
    - Latent samples to be re-encoded. These are the core data that undergo transformation through the specified VAE models.
    - Python dtype: `torch.Tensor`
    - Comfy dtype: `LATENT`
- **`tile_mode`**
    - Controls how tiling is applied during the re-encoding process, affecting how the latent samples are decoded and encoded.
    - Python dtype: `str`
    - Comfy dtype: `STRING`
- **`input_vae`**
    - The VAE model used for decoding the input latent samples.
    - Python dtype: `torch.nn.Module`
    - Comfy dtype: `VAE`
- **`output_vae`**
    - The VAE model used for encoding the output after processing the input latent samples.
    - Python dtype: `torch.nn.Module`
    - Comfy dtype: `VAE`
- **`tile_size`**
    - Specifies the size of tiles used when tiling is enabled, affecting the granularity of the process.
    - Python dtype: `int`
    - Comfy dtype: `INT`
## Output types
- **`latent`**
    - The re-encoded latent samples after processing through the input and output VAE models.
    - Python dtype: `torch.Tensor`
    - Comfy dtype: `LATENT`
## Usage tips
- Infra type: `GPU`
- Common nodes: unknown


## Source code
```python
class ReencodeLatent:
    @classmethod
    def INPUT_TYPES(s):
        return {"required": {
                        "samples": ("LATENT", ),
                        "tile_mode": (["None", "Both", "Decode(input) only", "Encode(output) only"],),
                        "input_vae": ("VAE", ),
                        "output_vae": ("VAE", ),
                        "tile_size": ("INT", {"default": 512, "min": 320, "max": 4096, "step": 64}),
                    },
                }

    CATEGORY = "ImpactPack/Util"

    RETURN_TYPES = ("LATENT", )
    FUNCTION = "doit"

    def doit(self, samples, tile_mode, input_vae, output_vae, tile_size=512):
        if tile_mode in ["Both", "Decode(input) only"]:
            pixels = nodes.VAEDecodeTiled().decode(input_vae, samples, tile_size)[0]
        else:
            pixels = nodes.VAEDecode().decode(input_vae, samples)[0]

        if tile_mode in ["Both", "Encode(output) only"]:
            return nodes.VAEEncodeTiled().encode(output_vae, pixels, tile_size)
        else:
            return nodes.VAEEncode().encode(output_vae, pixels)

```
