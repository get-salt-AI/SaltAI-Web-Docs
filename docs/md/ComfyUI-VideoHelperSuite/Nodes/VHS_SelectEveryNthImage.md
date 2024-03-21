# Select Every Nth Image 🎥🅥🅗🅢
## Documentation
- Class name: `VHS_SelectEveryNthImage`
- Category: `Video Helper Suite 🎥🅥🅗🅢/image`
- Output node: `False`

The `VHS_SelectEveryNthImage` node selects every Nth image from a batch of images, allowing for the thinning of image sequences or datasets. This can be useful for reducing the amount of data to process or for selecting a subset of images that meet a specific criterion (e.g., every 5th frame of a video).
## Input types
### Required
- **`images`**
    - The batch of images from which every Nth image will be selected. This input is crucial for determining the subset of images to be processed or analyzed.
    - Python dtype: `torch.Tensor`
    - Comfy dtype: `IMAGE`
- **`select_every_nth`**
    - Determines the interval at which images are selected from the batch. For example, a value of 3 means every 3rd image in the batch will be selected. This parameter allows for flexible control over the density of the resulting image subset.
    - Python dtype: `int`
    - Comfy dtype: `INT`
## Output types
- **`image`**
    - The subset of images obtained by selecting every Nth image from the input batch. This output is useful for downstream processing or analysis that requires a thinned version of the original image batch.
    - Python dtype: `torch.Tensor`
    - Comfy dtype: `IMAGE`
- **`int`**
    - The total number of images selected from the input batch. This output provides information on the size of the resulting subset of images.
    - Python dtype: `int`
    - Comfy dtype: `INT`
## Usage tips
- Infra type: `GPU`
- Common nodes: unknown


## Source code
```python
class SelectEveryNthImage:
    @classmethod
    def INPUT_TYPES(s):
        return {
                "required": {
                    "images": ("IMAGE",),
                    "select_every_nth": ("INT", {"default": 1, "min": 1, "step": 1}),
                },
            }
    
    CATEGORY = "Video Helper Suite 🎥🅥🅗🅢/image"

    RETURN_TYPES = ("IMAGE", "INT",)
    RETURN_NAMES = ("IMAGE", "count",)
    FUNCTION = "select_images"

    def select_images(self, images: Tensor, select_every_nth: int):
        sub_images = images[0::select_every_nth]
        return (sub_images, sub_images.size(0))

```
