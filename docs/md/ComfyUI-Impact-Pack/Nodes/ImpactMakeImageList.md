# Make Image List
## Documentation
- Class name: `ImpactMakeImageList`
- Category: `ImpactPack/Util`
- Output node: `False`

The `ImpactMakeImageList` node is designed to aggregate multiple images into a list. It accepts an arbitrary number of images as input and returns a list containing all the provided images. This functionality is useful for scenarios where handling a collection of images as a single entity is required, such as batch processing or when passing images through a pipeline that expects a list format.
## Input types
### Required
- **`image1`**
    - The primary image to be included in the list. This parameter serves as the starting point for the list of images, with additional images appended as they are provided.
    - Python dtype: `torch.Tensor`
    - Comfy dtype: `IMAGE`
## Output types
- **`image`**
    - A list of images aggregated from the input. This list is the primary output of the node, allowing for further processing or analysis of the images as a collective group.
    - Python dtype: `List[torch.Tensor]`
    - Comfy dtype: `IMAGE`
## Usage tips
- Infra type: `CPU`
- Common nodes: `ImageListToImageBatch`


## Source code
```python
class MakeImageList:
    @classmethod
    def INPUT_TYPES(s):
        return {"required": {"image1": ("IMAGE",), }}

    RETURN_TYPES = ("IMAGE",)
    OUTPUT_IS_LIST = (True,)
    FUNCTION = "doit"

    CATEGORY = "ImpactPack/Util"

    def doit(self, **kwargs):
        images = []

        for k, v in kwargs.items():
            images.append(v)

        return (images, )

```
