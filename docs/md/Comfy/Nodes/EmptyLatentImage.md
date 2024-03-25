# Empty Latent Image
## Documentation
- Class name: `EmptyLatentImage`
- Category: `latent`
- Output node: `False`

The EmptyLatentImage node is designed to generate a blank latent space representation with specified dimensions and batch size. This node serves as a foundational step in generating or manipulating images in latent space, providing a starting point for further image synthesis or modification processes.
## Input types
### Required
- **`width`**
    - Comfy dtype: `INT`
    - Specifies the width of the latent image to be generated. This parameter directly influences the spatial dimensions of the resulting latent representation.
    - Python dtype: `int`
- **`height`**
    - Comfy dtype: `INT`
    - Determines the height of the latent image to be generated. This parameter is crucial for defining the spatial dimensions of the latent space representation.
    - Python dtype: `int`
- **`batch_size`**
    - Comfy dtype: `INT`
    - Controls the number of latent images to be generated in a single batch. This allows for the generation of multiple latent representations simultaneously, facilitating batch processing.
    - Python dtype: `int`
## Output types
- **`latent`**
    - Comfy dtype: `LATENT`
    - The output is a tensor representing a batch of blank latent images, serving as a base for further image generation or manipulation in latent space.
    - Python dtype: `torch.Tensor`
## Usage tips
- Infra type: `GPU`
- Common nodes: `KSampler,SamplerCustom,KSamplerAdvanced,CR Module Pipe Loader,KSampler Adv. (Efficient),ImpactKSamplerBasicPipe,Anything Everywhere,Reroute,KRestartSamplerAdv,LatentComposite`


## Source code
```python
class EmptyLatentImage:
    def __init__(self):
        self.device = comfy.model_management.intermediate_device()

    @classmethod
    def INPUT_TYPES(s):
        return {"required": { "width": ("INT", {"default": 512, "min": 16, "max": MAX_RESOLUTION, "step": 8}),
                              "height": ("INT", {"default": 512, "min": 16, "max": MAX_RESOLUTION, "step": 8}),
                              "batch_size": ("INT", {"default": 1, "min": 1, "max": 4096})}}
    RETURN_TYPES = ("LATENT",)
    FUNCTION = "generate"

    CATEGORY = "latent"

    def generate(self, width, height, batch_size=1):
        latent = torch.zeros([batch_size, 4, height // 8, width // 8], device=self.device)
        return ({"samples":latent}, )

```
