# Rebatch Images
## Documentation
- Class name: `RebatchImages`
- Category: `image/batch`
- Output node: `False`

The `RebatchImages` node is designed to reorganize a list of image batches into a new list of image batches with a specified batch size. It iterates through each image in the input batches, flattens them into a single list of images, and then regroups these images into new batches according to the provided batch size. This process is useful for adjusting the batch size of image data for processing or inference in machine learning models.
## Input types
### Required
- **`images`**
    - A list of image batches to be rebatched. This input is crucial for determining the new organization of images across the output batches.
    - Python dtype: `List[torch.Tensor]`
    - Comfy dtype: `IMAGE`
- **`batch_size`**
    - The desired size for the new image batches. This parameter directly influences the number of images in each output batch, thereby affecting the granularity of processing or inference.
    - Python dtype: `int`
    - Comfy dtype: `INT`
## Output types
- **`image`**
    - A list of new image batches, each containing a number of images specified by the `batch_size` parameter. This output facilitates flexible batch processing in subsequent operations.
    - Python dtype: `List[torch.Tensor]`
    - Comfy dtype: `IMAGE`
## Usage tips
- Infra type: `GPU`
- Common nodes: unknown


## Source code
```python
class ImageRebatch:
    @classmethod
    def INPUT_TYPES(s):
        return {"required": { "images": ("IMAGE",),
                              "batch_size": ("INT", {"default": 1, "min": 1, "max": 4096}),
                              }}
    RETURN_TYPES = ("IMAGE",)
    INPUT_IS_LIST = True
    OUTPUT_IS_LIST = (True, )

    FUNCTION = "rebatch"

    CATEGORY = "image/batch"

    def rebatch(self, images, batch_size):
        batch_size = batch_size[0]

        output_list = []
        all_images = []
        for img in images:
            for i in range(img.shape[0]):
                all_images.append(img[i:i+1])

        for i in range(0, len(all_images), batch_size):
            output_list.append(torch.cat(all_images[i:i+batch_size], dim=0))

        return (output_list,)

```
