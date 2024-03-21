# VAE Decode Batched 🎥🅥🅗🅢
## Documentation
- Class name: `VHS_VAEDecodeBatched`
- Category: `Video Helper Suite 🎥🅥🅗🅢/batched nodes`
- Output node: `False`

This node performs batched decoding of latent representations into images using a specified VAE model. It processes the latent samples in smaller batches to manage memory usage effectively and concatenates the decoded images into a single tensor.
## Input types
### Required
- **`samples`**
    - The latent representations to be decoded into images. This input is crucial for generating the final output images from their compressed latent forms.
    - Python dtype: `torch.Tensor`
    - Comfy dtype: `LATENT`
- **`vae`**
    - The VAE model used for decoding the latent representations into images. It defines the specific architecture and parameters for the decoding process.
    - Python dtype: `torch.nn.Module`
    - Comfy dtype: `VAE`
- **`per_batch`**
    - Specifies the number of samples to be processed in each batch. This allows for control over memory usage and computational load during the decoding process.
    - Python dtype: `int`
    - Comfy dtype: `INT`
## Output types
- **`image`**
    - The decoded images generated from the latent representations. This output is a tensor containing all the images concatenated along the batch dimension.
    - Python dtype: `torch.Tensor`
    - Comfy dtype: `IMAGE`
## Usage tips
- Infra type: `GPU`
- Common nodes: unknown


## Source code
```python
class VAEDecodeBatched:
    @classmethod
    def INPUT_TYPES(s):
        return {
            "required": {
                "samples": ("LATENT", ),
                "vae": ("VAE", ),
                "per_batch": ("INT", {"default": 16, "min": 1})
                }
            }
    
    CATEGORY = "Video Helper Suite 🎥🅥🅗🅢/batched nodes"

    RETURN_TYPES = ("IMAGE",)
    FUNCTION = "decode"

    def decode(self, vae, samples, per_batch):
        decoded = []
        for start_idx in range(0, samples["samples"].shape[0], per_batch):
            decoded.append(vae.decode(samples["samples"][start_idx:start_idx+per_batch]))
        return (torch.cat(decoded, dim=0), )

```
