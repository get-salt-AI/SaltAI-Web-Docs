# Edit BasicPipe
## Documentation
- Class name: `EditBasicPipe`
- Category: `ImpactPack/Pipe`
- Output node: `False`

The EditBasicPipe node allows for the modification of an existing basic pipeline by optionally updating its components such as model, clip, VAE, and conditioning parameters. This enables customization and fine-tuning of the pipeline based on specific requirements.
## Input types
### Required
- **`basic_pipe`**
    - The basic pipeline to be modified. It consists of a model, clip, VAE, and conditioning parameters for positive and negative scenarios.
    - Python dtype: `Tuple[torch.nn.Module, Any, Any, str, str]`
    - Comfy dtype: `BASIC_PIPE`
### Optional
- **`model`**
    - Optional. A new model to replace the existing one in the basic pipeline, allowing for customization.
    - Python dtype: `Optional[torch.nn.Module]`
    - Comfy dtype: `MODEL`
- **`clip`**
    - Optional. A new CLIP model to replace the existing one in the basic pipeline, enhancing its capabilities.
    - Python dtype: `Optional[Any]`
    - Comfy dtype: `CLIP`
- **`vae`**
    - Optional. A new VAE model to replace the existing one in the basic pipeline, enabling different generative capabilities.
    - Python dtype: `Optional[Any]`
    - Comfy dtype: `VAE`
- **`positive`**
    - Optional. New positive conditioning to refine the generation towards desired attributes.
    - Python dtype: `Optional[str]`
    - Comfy dtype: `CONDITIONING`
- **`negative`**
    - Optional. New negative conditioning to steer the generation away from undesired attributes.
    - Python dtype: `Optional[str]`
    - Comfy dtype: `CONDITIONING`
## Output types
- **`basic_pipe`**
    - The modified basic pipeline, potentially updated with new model, clip, VAE, and conditioning parameters.
    - Python dtype: `Tuple[torch.nn.Module, Any, Any, str, str]`
    - Comfy dtype: `BASIC_PIPE`
## Usage tips
- Infra type: `CPU`
- Common nodes: `ImpactKSamplerBasicPipe,DetailerForEachDebugPipe`


## Source code
```python
class EditBasicPipe:
    @classmethod
    def INPUT_TYPES(s):
        return {
                "required": {"basic_pipe": ("BASIC_PIPE",), },
                "optional": {
                     "model": ("MODEL",),
                     "clip": ("CLIP",),
                     "vae": ("VAE",),
                     "positive": ("CONDITIONING",),
                     "negative": ("CONDITIONING",),
                     },
                }

    RETURN_TYPES = ("BASIC_PIPE", )
    RETURN_NAMES = ("basic_pipe", )
    FUNCTION = "doit"

    CATEGORY = "ImpactPack/Pipe"

    def doit(self, basic_pipe, model=None, clip=None, vae=None, positive=None, negative=None):
        res_model, res_clip, res_vae, res_positive, res_negative = basic_pipe

        if model is not None:
            res_model = model

        if clip is not None:
            res_clip = clip

        if vae is not None:
            res_vae = vae

        if positive is not None:
            res_positive = positive

        if negative is not None:
            res_negative = negative

        pipe = res_model, res_clip, res_vae, res_positive, res_negative

        return (pipe, )

```
