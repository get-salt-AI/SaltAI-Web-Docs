# Image batch to Image List
## Documentation
- Class name: `ImpactImageBatchToImageList`
- Category: `ImpactPack/Util`
- Output node: `False`

This node converts a batch of images into a list of individual images. It is useful for processing scenarios where operations need to be applied on a per-image basis after batch processing.
## Input types
### Required
- **`image`**
    - The input image batch to be split into individual images. This is crucial for operations that require handling images one at a time.
    - Python dtype: `torch.Tensor`
    - Comfy dtype: `IMAGE`
## Output types
- **`image`**
    - A list of individual images extracted from the input batch. This allows for subsequent operations to be applied to each image separately.
    - Python dtype: `List[torch.Tensor]`
    - Comfy dtype: `IMAGE`
## Usage tips
- Infra type: `GPU`
- Common nodes: `TilePreprocessor,VAEEncode,PreviewImage`


## Source code
```python
class ImageBatchToImageList:
    @classmethod
    def INPUT_TYPES(s):
        return {"required": {"image": ("IMAGE",), }}

    RETURN_TYPES = ("IMAGE",)
    OUTPUT_IS_LIST = (True,)
    FUNCTION = "doit"

    CATEGORY = "ImpactPack/Util"

    def doit(self, image):
        images = [image[i:i + 1, ...] for i in range(image.shape[0])]
        return (images, )

```
