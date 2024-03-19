# LatentSubtract
## Documentation
- Class name: `LatentSubtract`
- Category: `latent/advanced`
- Output node: `False`

The LatentSubtract node performs element-wise subtraction between two latent representations. It ensures that the shapes of the latents are compatible before performing the subtraction, adjusting the second latent's shape if necessary.
## Input types
### Required
- **`samples1`**
    - The first latent representation to be subtracted from. It serves as the base for the subtraction operation.
    - Python dtype: `Dict[str, torch.Tensor]`
    - Comfy dtype: `LATENT`
- **`samples2`**
    - The second latent representation to be subtracted. This latent is adjusted to match the shape of the first latent before the subtraction.
    - Python dtype: `Dict[str, torch.Tensor]`
    - Comfy dtype: `LATENT`
## Output types
- **`latent`**
    - The result of the element-wise subtraction between the two latent representations.
    - Python dtype: `Tuple[Dict[str, torch.Tensor]]`
    - Comfy dtype: `LATENT`
## Usage tips
- Infra type: `GPU`
- Common nodes: unknown


## Source code
```python
class LatentSubtract:
    @classmethod
    def INPUT_TYPES(s):
        return {"required": { "samples1": ("LATENT",), "samples2": ("LATENT",)}}

    RETURN_TYPES = ("LATENT",)
    FUNCTION = "op"

    CATEGORY = "latent/advanced"

    def op(self, samples1, samples2):
        samples_out = samples1.copy()

        s1 = samples1["samples"]
        s2 = samples2["samples"]

        s2 = reshape_latent_to(s1.shape, s2)
        samples_out["samples"] = s1 - s2
        return (samples_out,)

```
