# DetailerPipe -> BasicPipe
## Documentation
- Class name: `DetailerPipeToBasicPipe`
- Category: `ImpactPack/Pipe`
- Output node: `False`

The `DetailerPipeToBasicPipe` node transforms a detailer pipe into two basic pipes: a base basic pipe and a refiner basic pipe. This process involves extracting and reorganizing components from the input detailer pipe to form the output basic pipes.
## Input types
### Required
- **`detailer_pipe`**
    - The detailer pipe input is a complex structure that encapsulates models, clips, VAEs, and conditioning information for both base and refiner configurations. It is essential for constructing the basic pipes.
    - Python dtype: `Tuple[torch.nn.Module, Any, Any, Any, Any, Any, Any, Any, Any, Any, torch.nn.Module, Any, Any, Any]`
    - Comfy dtype: `DETAILER_PIPE`
## Output types
- **`basic_pipe`**
    - The refiner basic pipe includes a refiner model, clip, VAE, and positive and negative conditioning information, tailored for refinement tasks.
    - Python dtype: `Tuple[torch.nn.Module, Any, Any, Any, Any]`
    - Comfy dtype: `BASIC_PIPE`
## Usage tips
- Infra type: `CPU`
- Common nodes: `EditBasicPipe`


## Source code
```python
class DetailerPipeToBasicPipe:
    @classmethod
    def INPUT_TYPES(s):
        return {"required": {"detailer_pipe": ("DETAILER_PIPE",), }}

    RETURN_TYPES = ("BASIC_PIPE", "BASIC_PIPE")
    RETURN_NAMES = ("base_basic_pipe", "refiner_basic_pipe")
    FUNCTION = "doit"

    CATEGORY = "ImpactPack/Pipe"

    def doit(self, detailer_pipe):
        model, clip, vae, positive, negative, _, _, _, _, _, refiner_model, refiner_clip, refiner_positive, refiner_negative = detailer_pipe
        pipe = model, clip, vae, positive, negative
        refiner_pipe = refiner_model, refiner_clip, vae, refiner_positive, refiner_negative
        return (pipe, refiner_pipe)

```
