# Get Mask Count 🎥🅥🅗🅢
## Documentation
- Class name: `VHS_GetMaskCount`
- Category: `Video Helper Suite 🎥🅥🅗🅢/mask`
- Output node: `False`

The `VHS_GetMaskCount` node is designed to count the number of masks in a given batch. It takes a batch of masks as input and returns the total count of masks present. This functionality is crucial for operations that require knowledge of the batch size, such as splitting or merging batches, and ensures efficient processing in workflows involving multiple masks.
## Input types
### Required
- **`mask`**
    - The `mask` parameter represents the batch of masks for which the count is to be determined. It is essential for calculating the total number of masks present in the batch, enabling further processing or analysis.
    - Python dtype: `torch.Tensor`
    - Comfy dtype: `MASK`
## Output types
- **`int`**
    - The `count` parameter indicates the total number of masks present in the input batch. This information is vital for managing batches of masks in various processing workflows.
    - Python dtype: `int`
    - Comfy dtype: `INT`
## Usage tips
- Infra type: `GPU`
- Common nodes: unknown


## Source code
```python
class GetMaskCount:
    @classmethod
    def INPUT_TYPES(s):
        return {
            "required": {
                "mask": ("MASK",),
            }
        }
    
    CATEGORY = "Video Helper Suite 🎥🅥🅗🅢/mask"

    RETURN_TYPES = ("INT",)
    RETURN_NAMES = ("count",)
    FUNCTION = "count_input"

    def count_input(self, mask: Tensor):
        return (mask.size(0),)

```
