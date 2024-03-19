# StableCascade_EmptyLatentImage
## Documentation
- Class name: `StableCascade_EmptyLatentImage`
- Category: `_for_testing/stable_cascade`
- Output node: `False`

The `StableCascade_EmptyLatentImage` node generates two sets of empty latent images (c_latent and b_latent) with specified dimensions and batch size. The dimensions of these latent images are determined by the input width, height, compression factor, and batch size. This node is primarily used for initializing the latent space for further processing in a stable cascade model pipeline.
## Input types
### Required
- **`width`**
    - Specifies the width of the generated latent images. It directly influences the dimensions of the output latent tensors.
    - Python dtype: `int`
    - Comfy dtype: `INT`
- **`height`**
    - Determines the height of the generated latent images, affecting the dimensions of the output latent tensors.
    - Python dtype: `int`
    - Comfy dtype: `INT`
- **`compression`**
    - The compression factor used to calculate the dimensions of the c_latent images. A higher compression value results in smaller c_latent dimensions.
    - Python dtype: `int`
    - Comfy dtype: `INT`
- **`batch_size`**
    - Defines the number of latent images to generate in one batch. It directly affects the first dimension of the output latent tensors.
    - Python dtype: `int`
    - Comfy dtype: `INT`
## Output types
- **`latent`**
    - The second set of generated empty latent images, with dimensions reduced by a factor of 4 regardless of the compression input.
    - Python dtype: `torch.Tensor`
    - Comfy dtype: `LATENT`
## Usage tips
- Infra type: `GPU`
- Common nodes: unknown


## Source code
```python
class StableCascade_EmptyLatentImage:
    def __init__(self, device="cpu"):
        self.device = device

    @classmethod
    def INPUT_TYPES(s):
        return {"required": {
            "width": ("INT", {"default": 1024, "min": 256, "max": nodes.MAX_RESOLUTION, "step": 8}),
            "height": ("INT", {"default": 1024, "min": 256, "max": nodes.MAX_RESOLUTION, "step": 8}),
            "compression": ("INT", {"default": 42, "min": 4, "max": 128, "step": 1}),
            "batch_size": ("INT", {"default": 1, "min": 1, "max": 4096})
        }}
    RETURN_TYPES = ("LATENT", "LATENT")
    RETURN_NAMES = ("stage_c", "stage_b")
    FUNCTION = "generate"

    CATEGORY = "_for_testing/stable_cascade"

    def generate(self, width, height, compression, batch_size=1):
        c_latent = torch.zeros([batch_size, 16, height // compression, width // compression])
        b_latent = torch.zeros([batch_size, 4, height // 4, width // 4])
        return ({
            "samples": c_latent,
        }, {
            "samples": b_latent,
        })

```
