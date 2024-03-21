# Get Image Count 🎥🅥🅗🅢
## Documentation
- Class name: `VHS_GetImageCount`
- Category: `Video Helper Suite 🎥🅥🅗🅢/image`
- Output node: `False`

Counts the number of images in a given batch. This function is useful for understanding the size of a dataset or a batch of images processed or generated within a workflow.
## Input types
### Required
- **`images`**
    - Represents the batch of images for which the count is to be determined. The count of images directly influences the output of this node, providing a simple yet essential metric for batch processing.
    - Python dtype: `torch.Tensor`
    - Comfy dtype: `IMAGE`
## Output types
- **`int`**
    - The total number of images present in the input batch. This output is crucial for batch processing tasks, enabling dynamic adjustments and insights into the data being handled.
    - Python dtype: `int`
    - Comfy dtype: `INT`
## Usage tips
- Infra type: `CPU`
- Common nodes: unknown


## Source code
```python
class GetImageCount:
    @classmethod
    def INPUT_TYPES(s):
        return {
            "required": {
                "images": ("IMAGE",),
            }
        }
    
    CATEGORY = "Video Helper Suite 🎥🅥🅗🅢/image"

    RETURN_TYPES = ("INT",)
    RETURN_NAMES = ("count",)
    FUNCTION = "count_input"

    def count_input(self, images: Tensor):
        return (images.size(0),)

```
