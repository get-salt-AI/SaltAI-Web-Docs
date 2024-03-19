# LatentMultiply
## Documentation
- Class name: `LatentMultiply`
- Category: `latent/advanced`
- Output node: `False`

This node multiplies each element in a given latent representation by a specified multiplier. It's useful for adjusting the intensity or scale of features within the latent space.
## Input types
### Required
- **`samples`**
    - The latent representation to be modified. This input is crucial for defining the features or characteristics of the data that will be scaled.
    - Python dtype: `Dict[str, torch.Tensor]`
    - Comfy dtype: `LATENT`
- **`multiplier`**
    - A scalar value by which each element of the latent representation is multiplied. This allows for scaling the intensity of the latent features.
    - Python dtype: `float`
    - Comfy dtype: `FLOAT`
## Output types
- **`latent`**
    - The modified latent representation after each of its elements has been scaled by the multiplier.
    - Python dtype: `Dict[str, torch.Tensor]`
    - Comfy dtype: `LATENT`
## Usage tips
- Infra type: `GPU`
- Common nodes: unknown


## Source code
```python
class LatentMultiply:
    @classmethod
    def INPUT_TYPES(s):
        return {"required": { "samples": ("LATENT",),
                              "multiplier": ("FLOAT", {"default": 1.0, "min": -10.0, "max": 10.0, "step": 0.01}),
                             }}

    RETURN_TYPES = ("LATENT",)
    FUNCTION = "op"

    CATEGORY = "latent/advanced"

    def op(self, samples, multiplier):
        samples_out = samples.copy()

        s1 = samples["samples"]
        samples_out["samples"] = s1 * multiplier
        return (samples_out,)

```
