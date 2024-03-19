# Empty Latent Image
## Documentation
- Class name: `EmptyLatentImage`
- Category: `latent`
- Output node: `False`

This node generates an empty latent image tensor with specified dimensions and batch size. The tensor is filled with zeros and is intended for use as a placeholder or starting point in generative models.
## Input types
### Required
- **`width`**
    - Specifies the width of the latent image to be generated. It determines the horizontal dimension of the tensor. The width is crucial for defining the resolution and scale of the generated latent space, impacting the detail level of subsequent image generation.
    - Python dtype: `int`
    - Comfy dtype: `INT`
- **`height`**
    - Specifies the height of the latent image to be generated. It affects the vertical dimension of the tensor. Similar to width, the height is essential for setting the resolution and scale of the latent space, influencing the detail and proportions of generated images.
    - Python dtype: `int`
    - Comfy dtype: `INT`
- **`batch_size`**
    - Determines the number of latent images to generate in a single batch. This parameter allows for batch processing of images, enabling efficient generation and manipulation of multiple latent images simultaneously, which is vital for scaling up image generation processes.
    - Python dtype: `int`
    - Comfy dtype: `INT`
## Output types
- **`latent`**
    - The output is a tensor representing a batch of empty latent images. These images are initialized with zeros and are ready for further processing or manipulation.
    - Python dtype: `torch.Tensor`
    - Comfy dtype: `LATENT`
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
