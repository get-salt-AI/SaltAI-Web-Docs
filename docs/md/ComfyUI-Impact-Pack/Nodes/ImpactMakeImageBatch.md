# Make Image Batch
## Documentation
- Class name: `ImpactMakeImageBatch`
- Category: `ImpactPack/Util`
- Output node: `False`

The `ImpactMakeImageBatch` node is designed to combine multiple images into a single batch. It takes the first image as a reference and attempts to upscale any subsequent images to match its dimensions using a Lanczos filter for high-quality resampling. This process ensures that all images in the batch have the same dimensions, facilitating operations that require uniform image sizes. The node is part of the ImpactPack's utility functions, emphasizing its role in preprocessing images for batch processing in image enhancement or manipulation tasks.
## Input types
### Required
- **`image1`**
    - The primary image to which all other images will be matched and combined into a single batch. It serves as the reference for dimensions and scaling.
    - Python dtype: `torch.Tensor`
    - Comfy dtype: `IMAGE`
## Output types
- **`image`**
    - A single tensor containing all input images combined into a batch, with each image resized as necessary to match the dimensions of the first image.
    - Python dtype: `torch.Tensor`
    - Comfy dtype: `IMAGE`
## Usage tips
- Infra type: `GPU`
- Common nodes: `CR Image Grid Panel`


## Source code
```python
class MakeImageBatch:
    @classmethod
    def INPUT_TYPES(s):
        return {"required": {"image1": ("IMAGE",), }}

    RETURN_TYPES = ("IMAGE",)
    FUNCTION = "doit"

    CATEGORY = "ImpactPack/Util"

    def doit(self, **kwargs):
        image1 = kwargs['image1']
        del kwargs['image1']
        images = [value for value in kwargs.values()]

        if len(images) == 0:
            return (image1,)
        else:
            for image2 in images:
                if image1.shape[1:] != image2.shape[1:]:
                    image2 = comfy.utils.common_upscale(image2.movedim(-1, 1), image1.shape[2], image1.shape[1], "lanczos", "center").movedim(1, -1)
                image1 = torch.cat((image1, image2), dim=0)
            return (image1,)

```
