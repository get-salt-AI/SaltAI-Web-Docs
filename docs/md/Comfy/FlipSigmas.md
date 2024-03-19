# FlipSigmas
## Documentation
- Class name: `FlipSigmas`
- Category: `sampling/custom_sampling/sigmas`
- Output node: `False`

The `FlipSigmas` node is designed to manipulate a sequence of sigma values, which are typically used in diffusion models for controlling the noise level at each step of the generation process. It reverses the order of the sigma values and ensures that the first value is not zero by setting a minimum threshold if needed. This operation can be crucial for certain sampling strategies where the direction of processing or the starting point within the noise schedule needs to be adjusted.
## Input types
### Required
- **`sigmas`**
    - The `sigmas` parameter represents a sequence of sigma values that control the noise level at different steps of a diffusion process. Reversing the order of these values can be essential for altering the direction of the diffusion process, ensuring that the generation starts from a different noise level.
    - Python dtype: `torch.Tensor`
    - Comfy dtype: `SIGMAS`
## Output types
- **`sigmas`**
    - Returns the modified sequence of sigma values, with the order reversed and the first value adjusted if it was originally zero, to ensure it's non-zero.
    - Python dtype: `torch.Tensor`
    - Comfy dtype: `SIGMAS`
## Usage tips
- Infra type: `GPU`
- Common nodes: unknown


## Source code
```python
class FlipSigmas:
    @classmethod
    def INPUT_TYPES(s):
        return {"required":
                    {"sigmas": ("SIGMAS", ),
                     }
                }
    RETURN_TYPES = ("SIGMAS",)
    CATEGORY = "sampling/custom_sampling/sigmas"

    FUNCTION = "get_sigmas"

    def get_sigmas(self, sigmas):
        sigmas = sigmas.flip(0)
        if sigmas[0] == 0:
            sigmas[0] = 0.0001
        return (sigmas,)

```
