# LatentAdd
## Documentation
- Class name: `LatentAdd`
- Category: `latent/advanced`
- Output node: `False`

The `LatentAdd` node performs element-wise addition of two latent representations. This operation can be used to combine features or effects from two different latent spaces.
## Input types
### Required
- **`samples1`**
    - The first set of latent representations to be added. It serves as the base to which the second set is added.
    - Python dtype: `Dict[str, torch.Tensor]`
    - Comfy dtype: `LATENT`
- **`samples2`**
    - The second set of latent representations to be added to the first. It is reshaped to match the first set's dimensions if necessary.
    - Python dtype: `Dict[str, torch.Tensor]`
    - Comfy dtype: `LATENT`
## Output types
- **`latent`**
    - The result of element-wise addition of the two input latent representations. It combines the features or effects from both inputs.
    - Python dtype: `Tuple[Dict[str, torch.Tensor]]`
    - Comfy dtype: `LATENT`
## Usage tips
- Infra type: `GPU`
- Common nodes: unknown


## Source code
```python
class LatentAdd:
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
        samples_out["samples"] = s1 + s2
        return (samples_out,)

```
