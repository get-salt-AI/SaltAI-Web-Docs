# Remove Noise Mask
## Documentation
- Class name: `RemoveNoiseMask`
- Category: `ImpactPack/Util`
- Output node: `False`

The `RemoveNoiseMask` node likely focuses on processing or manipulating noise masks within images or latent representations. It may involve operations such as removing, reducing, or altering the noise mask applied to an image or a set of images, potentially to improve image quality or prepare the images for further processing steps.
## Input types
### Required
- **`samples`**
    - The `samples` parameter represents the images or latent representations that contain noise masks. This parameter is crucial as it provides the data on which the noise mask removal or manipulation operations will be performed.
    - Python dtype: `Dict[str, torch.Tensor]`
    - Comfy dtype: `LATENT`
## Output types
- **`latent`**
    - The output `samples` are the modified images or latent representations after the noise mask has been removed or altered. This indicates the node's primary function is to enhance the quality of the input by manipulating the noise mask.
    - Python dtype: `Dict[str, torch.Tensor]`
    - Comfy dtype: `LATENT`
## Usage tips
- Infra type: `GPU`
- Common nodes: unknown


## Source code
```python
class RemoveNoiseMask:
    @classmethod
    def INPUT_TYPES(s):
        return {"required": {"samples": ("LATENT",)}}

    RETURN_TYPES = ("LATENT",)
    FUNCTION = "doit"

    CATEGORY = "ImpactPack/Util"

    def doit(self, samples):
        res = {key: value for key, value in samples.items() if key != 'noise_mask'}
        return (res, )

```
