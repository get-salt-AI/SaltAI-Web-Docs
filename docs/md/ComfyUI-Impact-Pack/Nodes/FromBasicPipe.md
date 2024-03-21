# FromBasicPipe
## Documentation
- Class name: `FromBasicPipe`
- Category: `ImpactPack/Pipe`
- Output node: `False`

The `FromBasicPipe` node is designed to decompose a basic pipe structure into its individual components. It takes a basic pipe as input, which is a tuple containing a model, a clip, a VAE, and two conditioning elements (positive and negative), and returns these components separately.
## Input types
### Required
- **`basic_pipe`**
    - The basic pipe is a crucial input that encapsulates a model, a clip, a VAE, and two conditioning elements. It serves as the core structure for operations within the ImpactPack/Pipe category. The composition of this tuple directly influences the node's execution by determining the specific components to be extracted and returned, thus affecting the outcome of the decomposition process.
    - Python dtype: `Tuple[torch.nn.Module, Any, Any, str, str]`
    - Comfy dtype: `BASIC_PIPE`
## Output types
- **`model`**
    - The model component extracted from the basic pipe.
    - Python dtype: `torch.nn.Module`
    - Comfy dtype: `MODEL`
- **`clip`**
    - The clip component extracted from the basic pipe.
    - Python dtype: `Any`
    - Comfy dtype: `CLIP`
- **`vae`**
    - The VAE component extracted from the basic pipe.
    - Python dtype: `Any`
    - Comfy dtype: `VAE`
- **`conditioning`**
    - The negative conditioning element extracted from the basic pipe.
    - Python dtype: `str`
    - Comfy dtype: `CONDITIONING`
## Usage tips
- Infra type: `CPU`
- Common nodes: `UltimateSDUpscale,FaceDetailer,ToBasicPipe,KSampler,ControlNetApplyAdvanced,VAEEncode,VAEDecode,IterativeImageUpscale`


## Source code
```python
class FromBasicPipe:
    @classmethod
    def INPUT_TYPES(s):
        return {"required": {"basic_pipe": ("BASIC_PIPE",), }, }

    RETURN_TYPES = ("MODEL", "CLIP", "VAE", "CONDITIONING", "CONDITIONING")
    RETURN_NAMES = ("model", "clip", "vae", "positive", "negative")
    FUNCTION = "doit"

    CATEGORY = "ImpactPack/Pipe"

    def doit(self, basic_pipe):
        model, clip, vae, positive, negative = basic_pipe
        return model, clip, vae, positive, negative

```
