# SplitSigmas
## Documentation
- Class name: `SplitSigmas`
- Category: `sampling/custom_sampling/sigmas`
- Output node: `False`

The `SplitSigmas` node is designed to divide a sequence of sigmas into two parts based on a specified step. This operation is useful in scenarios where different processing or analysis is required for distinct segments of the sigma sequence.
## Input types
### Required
- **`sigmas`**
    - The sequence of sigmas to be split. This parameter is crucial for determining how the sequence will be divided into two parts.
    - Python dtype: `torch.Tensor`
    - Comfy dtype: `SIGMAS`
- **`step`**
    - The index at which the sigma sequence is split. The sequence is divided such that the first part includes sigmas up to and including this index, and the second part contains the remaining sigmas.
    - Python dtype: `int`
    - Comfy dtype: `INT`
## Output types
- **`sigmas`**
    - The second part of the split sigma sequence, starting from the specified step.
    - Python dtype: `torch.Tensor`
    - Comfy dtype: `SIGMAS`
## Usage tips
- Infra type: `CPU`
- Common nodes: `SamplerCustom`


## Source code
```python
class SplitSigmas:
    @classmethod
    def INPUT_TYPES(s):
        return {"required":
                    {"sigmas": ("SIGMAS", ),
                    "step": ("INT", {"default": 0, "min": 0, "max": 10000}),
                     }
                }
    RETURN_TYPES = ("SIGMAS","SIGMAS")
    CATEGORY = "sampling/custom_sampling/sigmas"

    FUNCTION = "get_sigmas"

    def get_sigmas(self, sigmas, step):
        sigmas1 = sigmas[:step + 1]
        sigmas2 = sigmas[step:]
        return (sigmas1, sigmas2)

```
