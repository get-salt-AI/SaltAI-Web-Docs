# Split Mask Batch 🎥🅥🅗🅢
## Documentation
- Class name: `VHS_SplitMasks`
- Category: `Video Helper Suite 🎥🅥🅗🅢/mask`
- Output node: `False`

The `VHS_SplitMasks` node is designed to divide a batch of masks into two groups based on a specified index. This operation allows for the separation of mask data into distinct sets, which can be useful in various image and video processing tasks where masks need to be handled or analyzed separately.
## Input types
### Required
- **`mask`**
    - The input mask tensor that is to be split into two groups. This parameter is crucial for defining the point of division within the batch of masks.
    - Python dtype: `torch.Tensor`
    - Comfy dtype: `MASK`
- **`split_index`**
    - An integer specifying the index at which the input mask tensor should be split. The choice of this index directly influences the composition of the resulting mask groups.
    - Python dtype: `int`
    - Comfy dtype: `INT`
## Output types
- **`mask`**
    - The second group of masks obtained after splitting the input tensor at the specified index.
    - Python dtype: `torch.Tensor`
    - Comfy dtype: `MASK`
- **`int`**
    - The count of masks in the second group.
    - Python dtype: `int`
    - Comfy dtype: `INT`
## Usage tips
- Infra type: `GPU`
- Common nodes: unknown


## Source code
```python
class SplitMasks:
    @classmethod
    def INPUT_TYPES(s):
        return {
                "required": {
                    "mask": ("MASK",),
                    "split_index": ("INT", {"default": 0, "step": 1, "min": -99999999999}),
                },
            }
    
    CATEGORY = "Video Helper Suite 🎥🅥🅗🅢/mask"

    RETURN_TYPES = ("MASK", "INT", "MASK", "INT")
    RETURN_NAMES = ("MASK_A", "A_count", "MASK_B", "B_count")
    FUNCTION = "split_masks"

    def split_masks(self, mask: Tensor, split_index: int):
        group_a = mask[:split_index]
        group_b = mask[split_index:]
        return (group_a, group_a.size(0), group_b, group_b.size(0))

```
