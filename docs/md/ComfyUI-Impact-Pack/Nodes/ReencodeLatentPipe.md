# Reencode Latent (pipe)
## Documentation
- Class name: `ReencodeLatentPipe`
- Category: `ImpactPack/Util`
- Output node: `False`

The `ReencodeLatentPipe` node is designed to re-encode latent representations of data. It takes samples in latent space, processes them through two basic pipelines (input and output), and re-encodes them based on the specified tile mode. This operation is useful for transforming latent representations using different encoding or decoding strategies, potentially enhancing or altering the data's characteristics for further applications.
## Input types
### Required
- **`samples`**
    - The latent samples to be re-encoded. These are the core data representations that the node operates on, serving as the input for the re-encoding process.
    - Python dtype: `torch.Tensor`
    - Comfy dtype: `LATENT`
- **`tile_mode`**
    - Specifies the mode of tiling to be applied during the re-encoding process. It affects how the input and output are encoded or decoded, allowing for flexible processing strategies.
    - Python dtype: `str`
    - Comfy dtype: `['None', 'Both', 'Decode(input) only', 'Encode(output) only']`
- **`input_basic_pipe`**
    - The basic pipeline through which the input samples are processed before re-encoding. It defines the initial transformations applied to the latent samples.
    - Python dtype: `Tuple`
    - Comfy dtype: `BASIC_PIPE`
- **`output_basic_pipe`**
    - The basic pipeline for processing the re-encoded samples. It specifies the transformations applied to the output of the re-encoding process.
    - Python dtype: `Tuple`
    - Comfy dtype: `BASIC_PIPE`
## Output types
- **`latent`**
    - The re-encoded latent samples. This output represents the transformed data after being processed through the input and output basic pipelines, according to the specified tile mode.
    - Python dtype: `torch.Tensor`
    - Comfy dtype: `LATENT`
## Usage tips
- Infra type: `GPU`
- Common nodes: unknown


## Source code
```python
class ReencodeLatentPipe:
    @classmethod
    def INPUT_TYPES(s):
        return {"required": {
                        "samples": ("LATENT", ),
                        "tile_mode": (["None", "Both", "Decode(input) only", "Encode(output) only"],),
                        "input_basic_pipe": ("BASIC_PIPE", ),
                        "output_basic_pipe": ("BASIC_PIPE", ),
                    },
                }

    CATEGORY = "ImpactPack/Util"

    RETURN_TYPES = ("LATENT", )
    FUNCTION = "doit"

    def doit(self, samples, tile_mode, input_basic_pipe, output_basic_pipe):
        _, _, input_vae, _, _ = input_basic_pipe
        _, _, output_vae, _, _ = output_basic_pipe
        return ReencodeLatent().doit(samples, tile_mode, input_vae, output_vae)

```
