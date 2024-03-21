# Duplicate Image Batch 🎥🅥🅗🅢
## Documentation
- Class name: `VHS_DuplicateImages`
- Category: `Video Helper Suite 🎥🅥🅗🅢/image`
- Output node: `False`

Duplicates a batch of images a specified number of times. This node is useful for creating larger datasets from a smaller number of images or for data augmentation purposes by repeating the images multiple times.
## Input types
### Required
- **`images`**
    - The input tensor containing the batch of images to be duplicated. This parameter is crucial for determining the content that will be replicated.
    - Python dtype: `Tensor`
    - Comfy dtype: `IMAGE`
- **`multiply_by`**
    - Specifies the number of times the input images should be duplicated. This affects the size of the output batch, directly influencing the amount of data generated.
    - Python dtype: `int`
    - Comfy dtype: `INT`
## Output types
- **`image`**
    - The output tensor containing the duplicated images. The size of this tensor is determined by the original batch size and the multiplication factor.
    - Python dtype: `Tensor`
    - Comfy dtype: `IMAGE`
- **`int`**
    - The total number of images in the output batch, providing a quick reference to the size of the generated dataset.
    - Python dtype: `int`
    - Comfy dtype: `INT`
## Usage tips
- Infra type: `GPU`
- Common nodes: unknown


## Source code
```python
class DuplicateImages:
    @classmethod
    def INPUT_TYPES(s):
        return {
            "required": {
                "images": ("IMAGE",),
                "multiply_by": ("INT", {"default": 1, "min": 1, "step": 1})
            }
        }
    
    CATEGORY = "Video Helper Suite 🎥🅥🅗🅢/image"

    RETURN_TYPES = ("IMAGE", "INT",)
    RETURN_NAMES = ("IMAGE", "count",)
    FUNCTION = "duplicate_input"

    def duplicate_input(self, images: Tensor, multiply_by: int):
        full_images = []
        for n in range(0, multiply_by):
            full_images.append(images)
        new_images = torch.cat(full_images, dim=0)
        return (new_images, new_images.size(0),)

```
