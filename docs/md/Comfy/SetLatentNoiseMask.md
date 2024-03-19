# Set Latent Noise Mask
## Documentation
- Class name: `SetLatentNoiseMask`
- Category: `latent/inpaint`
- Output node: `False`

This node applies a noise mask to a given set of latent samples. It modifies the input samples by adding a 'noise_mask' field, which is reshaped to match the dimensions of the latent samples.
## Input types
### Required
- **`samples`**
    - The latent samples to which the noise mask will be applied. This parameter is crucial for defining the data that will be modified.
    - Python dtype: `Dict[str, torch.Tensor]`
    - Comfy dtype: `LATENT`
- **`mask`**
    - The mask to be applied to the latent samples. It is reshaped to fit the dimensions of the samples, thereby affecting the final output by selectively applying noise.
    - Python dtype: `torch.Tensor`
    - Comfy dtype: `MASK`
## Output types
- **`latent`**
    - The modified latent samples with the applied noise mask. This output retains the original structure of the input samples but includes the added 'noise_mask' field.
    - Python dtype: `Tuple[Dict[str, torch.Tensor]]`
    - Comfy dtype: `LATENT`
## Usage tips
- Infra type: `GPU`
- Common nodes: `KSampler,KSampler (Efficient),Mute / Bypass Repeater (rgthree)`


## Source code
```python
class SetLatentNoiseMask:
    @classmethod
    def INPUT_TYPES(s):
        return {"required": { "samples": ("LATENT",),
                              "mask": ("MASK",),
                              }}
    RETURN_TYPES = ("LATENT",)
    FUNCTION = "set_mask"

    CATEGORY = "latent/inpaint"

    def set_mask(self, samples, mask):
        s = samples.copy()
        s["noise_mask"] = mask.reshape((-1, 1, mask.shape[-2], mask.shape[-1]))
        return (s,)

```
