# Split Image Batch 🎥🅥🅗🅢
## Documentation
- Class name: `VHS_SplitImages`
- Category: `Video Helper Suite 🎥🅥🅗🅢/image`
- Output node: `False`

The `VHS_SplitImages` node is designed to split a batch of images into two groups based on a specified index. This operation facilitates the manipulation of image batches by allowing users to separate them into distinct sets for further processing or analysis.
## Input types
### Required
- **`images`**
    - Represents the batch of images to be split. This parameter is crucial for determining how the images are divided into two groups.
    - Python dtype: `torch.Tensor`
    - Comfy dtype: `IMAGE`
- **`split_index`**
    - The index at which the batch of images is split. This parameter directly influences the composition of the resulting image groups, determining which images belong to each group.
    - Python dtype: `int`
    - Comfy dtype: `INT`
## Output types
- **`image`**
    - The second group of images obtained after the split, determined by the `split_index`.
    - Python dtype: `torch.Tensor`
    - Comfy dtype: `IMAGE`
- **`int`**
    - The number of images in the second group after the split.
    - Python dtype: `int`
    - Comfy dtype: `INT`
## Usage tips
- Infra type: `GPU`
- Common nodes: `SVD_img2vid_Conditioning,PreviewImage,SeargeIntegerMath,VHS_SplitImages,STMFNet VFI`


## Source code
```python
class SplitImages:
    @classmethod
    def INPUT_TYPES(s):
        return {
                "required": {
                    "images": ("IMAGE",),
                    "split_index": ("INT", {"default": 0, "step": 1, "min": -99999999999}),
                },
            }
    
    CATEGORY = "Video Helper Suite 🎥🅥🅗🅢/image"

    RETURN_TYPES = ("IMAGE", "INT", "IMAGE", "INT")
    RETURN_NAMES = ("IMAGE_A", "A_count", "IMAGE_B", "B_count")
    FUNCTION = "split_images"

    def split_images(self, images: Tensor, split_index: int):
        group_a = images[:split_index]
        group_b = images[split_index:]
        return (group_a, group_a.size(0), group_b, group_b.size(0))

```
