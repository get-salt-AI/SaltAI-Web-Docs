# IsMaskEmpty
## Documentation
- Class name: `IsMaskEmptyNode`
- Category: `segment_anything`
- Output node: `False`

This node checks if a given mask is entirely empty (i.e., all its elements are zero) and returns a number indicating the result. It's useful for determining whether any part of an image or area is covered by a mask.
## Input types
### Required
- **`mask`**
    - The mask to be checked for emptiness. This input is crucial for determining whether the mask covers any area or not.
    - Python dtype: `torch.Tensor`
    - Comfy dtype: `MASK`
## Output types
- **`number`**
    - A number indicating whether the mask is empty (1) or not (0). This numeric representation is useful for conditional operations based on mask coverage.
    - Python dtype: `int`
    - Comfy dtype: `NUMBER`
## Usage tips
- Infra type: `GPU`
- Common nodes: unknown

Often used in pipelines to determine if a selected area of an image is devoid of any features or objects, by checking if the mask applied to that area is entirely empty. This node takes a mask as input and outputs a number indicating whether the mask is empty (1) or not (0), which is crucial for conditional operations in image processing and segmentation tasks.
## Source code
```python
class IsMaskEmptyNode:
    @classmethod
    def INPUT_TYPES(s):
        return {
            "required": {
                "mask": ("MASK",),
            },
        }
    RETURN_TYPES = ["NUMBER"]
    RETURN_NAMES = ["boolean_number"]

    FUNCTION = "main"
    CATEGORY = "segment_anything"

    def main(self, mask):
        return (torch.all(mask == 0).int().item(), )

```
