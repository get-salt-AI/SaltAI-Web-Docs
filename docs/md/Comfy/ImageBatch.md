# Batch Images
## Documentation
- Class name: `ImageBatch`
- Category: `image`
- Output node: `False`

The `ImageBatch` node combines two images into a single batch. If the images have different sizes, it resizes the second image to match the first one's dimensions using bilinear interpolation before combining them.
## Input types
### Required
- **`image1`**
    - The first image to be included in the batch. It serves as the reference for the size to which the second image will be resized if necessary.
    - Python dtype: `torch.Tensor`
    - Comfy dtype: `IMAGE`
- **`image2`**
    - The second image to be included in the batch. It is resized to match the first image's dimensions if they differ, using bilinear interpolation.
    - Python dtype: `torch.Tensor`
    - Comfy dtype: `IMAGE`
## Output types
- **`image`**
    - The combined batch of the two input images.
    - Python dtype: `torch.Tensor`
    - Comfy dtype: `IMAGE`
## Usage tips
- Infra type: `GPU`
- Common nodes: `ImageBatch,IPAdapterApply,CR Batch Process Switch,PreviewImage,Preview Chooser`


## Source code
```python
class ImageBatch:

    @classmethod
    def INPUT_TYPES(s):
        return {"required": { "image1": ("IMAGE",), "image2": ("IMAGE",)}}

    RETURN_TYPES = ("IMAGE",)
    FUNCTION = "batch"

    CATEGORY = "image"

    def batch(self, image1, image2):
        if image1.shape[1:] != image2.shape[1:]:
            image2 = comfy.utils.common_upscale(image2.movedim(-1,1), image1.shape[2], image1.shape[1], "bilinear", "center").movedim(1,-1)
        s = torch.cat((image1, image2), dim=0)
        return (s,)

```
