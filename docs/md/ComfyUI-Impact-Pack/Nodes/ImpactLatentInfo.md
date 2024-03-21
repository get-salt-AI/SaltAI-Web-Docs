# ImpactLatentInfo
## Documentation
- Class name: `ImpactLatentInfo`
- Category: `ImpactPack/Logic/_for_test`
- Output node: `False`

The `ImpactLatentInfo` node processes a latent representation to extract its shape and modify dimensions to generate new shape values. It's used for testing within the ImpactPack logic category.
## Input types
### Required
- **`value`**
    - The latent representation to be processed. Its shape is used to calculate the output dimensions.
    - Python dtype: `Dict[str, torch.Tensor]`
    - Comfy dtype: `LATENT`
## Output types
- **`int`**
    - The channel dimension, derived from the latent shape but reordered in the output.
    - Python dtype: `int`
    - Comfy dtype: `INT`
## Usage tips
- Infra type: `CPU`
- Common nodes: unknown


## Source code
```python
class ImpactLatentInfo:
    @classmethod
    def INPUT_TYPES(cls):
        return {"required": {
                    "value": ("LATENT", ),
                    },
                }

    FUNCTION = "doit"

    CATEGORY = "ImpactPack/Logic/_for_test"

    RETURN_TYPES = ("INT", "INT", "INT", "INT")
    RETURN_NAMES = ("batch", "height", "width", "channel")

    def doit(self, value):
        shape = value['samples'].shape
        return (shape[0], shape[2] * 8, shape[3] * 8, shape[1])

```
