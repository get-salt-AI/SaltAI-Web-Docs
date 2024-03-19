# LatentInterpolate
## Documentation
- Class name: `LatentInterpolate`
- Category: `latent/advanced`
- Output node: `False`

The `LatentInterpolate` node performs interpolation between two sets of latent samples based on a specified ratio. It normalizes the samples, computes a weighted average based on the ratio, and scales the result according to the original magnitudes.
## Input types
### Required
- **`samples1`**
    - The first set of latent samples to interpolate from. It serves as the base for the interpolation process.
    - Python dtype: `Dict[str, torch.Tensor]`
    - Comfy dtype: `LATENT`
- **`samples2`**
    - The second set of latent samples to interpolate towards. It is combined with the first set based on the interpolation ratio.
    - Python dtype: `Dict[str, torch.Tensor]`
    - Comfy dtype: `LATENT`
- **`ratio`**
    - A floating-point value that determines the weight of each set of samples in the interpolation. A ratio of 0 yields the first set, while a ratio of 1 yields the second set.
    - Python dtype: `float`
    - Comfy dtype: `FLOAT`
## Output types
- **`latent`**
    - The interpolated latent samples, combining features from both input sets according to the specified ratio.
    - Python dtype: `Dict[str, torch.Tensor]`
    - Comfy dtype: `LATENT`
## Usage tips
- Infra type: `GPU`
- Common nodes: `KSampler (Efficient),Latent Noise Injection`


## Source code
```python
class LatentInterpolate:
    @classmethod
    def INPUT_TYPES(s):
        return {"required": { "samples1": ("LATENT",),
                              "samples2": ("LATENT",),
                              "ratio": ("FLOAT", {"default": 1.0, "min": 0.0, "max": 1.0, "step": 0.01}),
                              }}

    RETURN_TYPES = ("LATENT",)
    FUNCTION = "op"

    CATEGORY = "latent/advanced"

    def op(self, samples1, samples2, ratio):
        samples_out = samples1.copy()

        s1 = samples1["samples"]
        s2 = samples2["samples"]

        s2 = reshape_latent_to(s1.shape, s2)

        m1 = torch.linalg.vector_norm(s1, dim=(1))
        m2 = torch.linalg.vector_norm(s2, dim=(1))

        s1 = torch.nan_to_num(s1 / m1)
        s2 = torch.nan_to_num(s2 / m2)

        t = (s1 * ratio + s2 * (1.0 - ratio))
        mt = torch.linalg.vector_norm(t, dim=(1))
        st = torch.nan_to_num(t / mt)

        samples_out["samples"] = st * (m1 * ratio + m2 * (1.0 - ratio))
        return (samples_out,)

```
