# ToBasicPipe
## Documentation
- Class name: `ToBasicPipe`
- Category: `ImpactPack/Pipe`
- Output node: `False`

ToBasicPipe node constructs a basic pipeline from individual components such as a model, clip, VAE, and conditioning elements. It encapsulates these components into a single tuple, facilitating their collective management and usage in further processing.
## Input types
### Required
- **`model`**
    - The model component of the pipeline, essential for the generation or transformation process.
    - Python dtype: `MODEL`
    - Comfy dtype: `MODEL`
- **`clip`**
    - The CLIP component, used for semantic understanding and alignment between text and images.
    - Python dtype: `CLIP`
    - Comfy dtype: `CLIP`
- **`vae`**
    - The VAE component, crucial for encoding and decoding images in the pipeline.
    - Python dtype: `VAE`
    - Comfy dtype: `VAE`
- **`positive`**
    - Positive conditioning element, guiding the generation towards desired attributes.
    - Python dtype: `CONDITIONING`
    - Comfy dtype: `CONDITIONING`
- **`negative`**
    - Negative conditioning element, steering the generation away from undesired attributes.
    - Python dtype: `CONDITIONING`
    - Comfy dtype: `CONDITIONING`
## Output types
- **`basic_pipe`**
    - The constructed basic pipeline, encapsulating the model, clip, VAE, and conditioning elements.
    - Python dtype: `BASIC_PIPE`
    - Comfy dtype: `BASIC_PIPE`
## Usage tips
- Infra type: `CPU`
- Common nodes: `FromBasicPipe,ImpactKSamplerBasicPipe,Reroute,DetailerForEachDebugPipe,DetailerForEachPipeForAnimateDiff,SEGSDetailerForAnimateDiff,PixelKSampleUpscalerProviderPipe`


## Source code
```python
class ToBasicPipe:
    @classmethod
    def INPUT_TYPES(s):
        return {"required": {
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

    def doit(self, model, clip, vae, positive, negative):
        pipe = (model, clip, vae, positive, negative)
        return (pipe, )

```
