# Empty Latent Image (Big Batch) 🎭🅐🅓
## Documentation
- Class name: `ADE_EmptyLatentImageLarge`
- Category: `Animate Diff 🎭🅐🅓/extras`
- Output node: `False`

The `ADE_EmptyLatentImageLarge` node appears to be a hypothetical or custom node not directly referenced in the provided context. However, based on naming conventions and similar nodes in the context, it can be inferred that this node might be designed to generate a large empty latent image tensor. This tensor could serve as a placeholder or initial state for further processing in generative models, particularly in tasks involving image synthesis or manipulation at a larger scale than standard `EmptyLatentImage` nodes.
## Input types
### Required
- **`width`**
    - Specifies the width of the generated latent image. It determines the horizontal dimension of the tensor, affecting the size and aspect ratio of the resulting image.
    - Python dtype: `int`
    - Comfy dtype: `INT`
- **`height`**
    - Defines the height of the generated latent image. It influences the vertical dimension of the tensor, impacting the size and aspect ratio of the resulting image.
    - Python dtype: `int`
    - Comfy dtype: `INT`
- **`batch_size`**
    - Determines the number of latent images to generate in a single batch. This parameter allows for batch processing, enhancing computational efficiency.
    - Python dtype: `int`
    - Comfy dtype: `INT`
## Output types
- **`latent`**
    - The output is a tensor representing a batch of large empty latent images. These images are initialized to a specific state and can be used as a starting point for further image generation or manipulation processes.
    - Python dtype: `torch.Tensor`
    - Comfy dtype: `LATENT`
## Usage tips
- Infra type: `GPU`
- Common nodes: `BatchPromptScheduleLatentInput,KSampler`


## Source code
```python
class EmptyLatentImageLarge:
    def __init__(self, device="cpu"):
        self.device = device

    @classmethod
    def INPUT_TYPES(s):
        return {"required": { "width": ("INT", {"default": 512, "min": 64, "max": comfy_nodes.MAX_RESOLUTION, "step": 8}),
                              "height": ("INT", {"default": 512, "min": 64, "max": comfy_nodes.MAX_RESOLUTION, "step": 8}),
                              "batch_size": ("INT", {"default": 1, "min": 1, "max": 262144})}}
    RETURN_TYPES = ("LATENT",)
    FUNCTION = "generate"

    CATEGORY = "Animate Diff 🎭🅐🅓/extras"

    def generate(self, width, height, batch_size=1):
        latent = torch.zeros([batch_size, 4, height // 8, width // 8])
        return ({"samples":latent}, )

```
