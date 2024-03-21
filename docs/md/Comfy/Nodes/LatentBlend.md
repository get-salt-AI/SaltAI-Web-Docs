# Latent Blend
## Documentation
- Class name: `LatentBlend`
- Category: `_for_testing`
- Output node: `False`

The `LatentBlend` node blends two sets of latent representations (samples) based on a specified blend factor. It supports different blending modes to combine the features of the input samples in various ways, allowing for flexible manipulation of latent spaces.
## Input types
### Required
- **`samples1`**
    - The first set of latent representations to be blended. It plays a crucial role in determining the base features of the output.
    - Python dtype: `Dict[str, torch.Tensor]`
    - Comfy dtype: `LATENT`
- **`samples2`**
    - The second set of latent representations to be blended with the first. It contributes to the final blend by adding or modifying features based on the blend mode and factor.
    - Python dtype: `Dict[str, torch.Tensor]`
    - Comfy dtype: `LATENT`
- **`blend_factor`**
    - A value between 0 and 1 that determines the weight of each input sample in the final blend. A higher blend factor gives more weight to the features of the first set of samples.
    - Python dtype: `float`
    - Comfy dtype: `FLOAT`
## Output types
- **`latent`**
    - The blended latent representations, combining features from both input sets according to the blend factor and mode.
    - Python dtype: `Dict[str, torch.Tensor]`
    - Comfy dtype: `LATENT`
## Usage tips
- Infra type: `GPU`
- Common nodes: `NNLatentUpscale,SamplerCustom,KSamplerAdvanced,Mute / Bypass Repeater (rgthree)`


## Source code
```python
class LatentBlend:
    @classmethod
    def INPUT_TYPES(s):
        return {"required": {
            "samples1": ("LATENT",),
            "samples2": ("LATENT",),
            "blend_factor": ("FLOAT", {
                "default": 0.5,
                "min": 0,
                "max": 1,
                "step": 0.01
            }),
        }}

    RETURN_TYPES = ("LATENT",)
    FUNCTION = "blend"

    CATEGORY = "_for_testing"

    def blend(self, samples1, samples2, blend_factor:float, blend_mode: str="normal"):

        samples_out = samples1.copy()
        samples1 = samples1["samples"]
        samples2 = samples2["samples"]

        if samples1.shape != samples2.shape:
            samples2.permute(0, 3, 1, 2)
            samples2 = comfy.utils.common_upscale(samples2, samples1.shape[3], samples1.shape[2], 'bicubic', crop='center')
            samples2.permute(0, 2, 3, 1)

        samples_blended = self.blend_mode(samples1, samples2, blend_mode)
        samples_blended = samples1 * blend_factor + samples_blended * (1 - blend_factor)
        samples_out["samples"] = samples_blended
        return (samples_out,)

    def blend_mode(self, img1, img2, mode):
        if mode == "normal":
            return img2
        else:
            raise ValueError(f"Unsupported blend mode: {mode}")

```
