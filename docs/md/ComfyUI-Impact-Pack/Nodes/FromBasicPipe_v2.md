# FromBasicPipe_v2
## Documentation
- Class name: `FromBasicPipe_v2`
- Category: `ImpactPack/Pipe`
- Output node: `False`

The `FromBasicPipe_v2` node decomposes a basic pipe into its constituent components, including the basic pipe itself and its individual elements such as model, clip, VAE, and conditioning factors for positive and negative scenarios. This allows for direct access and manipulation of these components.
## Input types
### Required
- **`basic_pipe`**
    - The `basic_pipe` parameter represents a collection of core components used in generative models, including a model, clip, VAE, and conditioning factors. It serves as the input structure from which individual elements are extracted.
    - Python dtype: `Tuple[torch.nn.Module, Any, Any, Any, Any]`
    - Comfy dtype: `BASIC_PIPE`
## Output types
- **`basic_pipe`**
    - Returns the original basic pipe as received in the input.
    - Python dtype: `Tuple[torch.nn.Module, Any, Any, Any, Any]`
    - Comfy dtype: `BASIC_PIPE`
- **`model`**
    - The generative model component extracted from the basic pipe.
    - Python dtype: `torch.nn.Module`
    - Comfy dtype: `MODEL`
- **`clip`**
    - The CLIP model component extracted from the basic pipe.
    - Python dtype: `Any`
    - Comfy dtype: `CLIP`
- **`vae`**
    - The VAE model component extracted from the basic pipe.
    - Python dtype: `Any`
    - Comfy dtype: `VAE`
- **`conditioning`**
    - The negative conditioning factor extracted from the basic pipe.
    - Python dtype: `Any`
    - Comfy dtype: `CONDITIONING`
## Usage tips
- Infra type: `CPU`
- Common nodes: `ToIPAdapterPipe //Inspire,EditBasicPipe`


## Source code
```python
class FromBasicPipe_v2:
    @classmethod
    def INPUT_TYPES(s):
        return {"required": {"basic_pipe": ("BASIC_PIPE",), }, }

    RETURN_TYPES = ("BASIC_PIPE", "MODEL", "CLIP", "VAE", "CONDITIONING", "CONDITIONING")
    RETURN_NAMES = ("basic_pipe", "model", "clip", "vae", "positive", "negative")
    FUNCTION = "doit"

    CATEGORY = "ImpactPack/Pipe"

    def doit(self, basic_pipe):
        model, clip, vae, positive, negative = basic_pipe
        return basic_pipe, model, clip, vae, positive, negative

```
