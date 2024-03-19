# ImageFromBatch
## Documentation
- Class name: `ImageFromBatch`
- Category: `image/batch`
- Output node: `False`

The `ImageFromBatch` node extracts a specific range of images from a batch based on the provided batch index and length. It allows for selective retrieval of images from a larger collection, facilitating operations like image processing on subsets of a batch.
## Input types
### Required
- **`image`**
    - The batch of images from which a subset will be extracted. This parameter is crucial for specifying the source of images.
    - Python dtype: `torch.Tensor`
    - Comfy dtype: `IMAGE`
- **`batch_index`**
    - The starting index within the batch from which the extraction begins. This parameter determines the starting point of the subset.
    - Python dtype: `int`
    - Comfy dtype: `INT`
- **`length`**
    - The number of images to extract from the batch, starting from the batch index. This parameter defines the size of the subset to be extracted.
    - Python dtype: `int`
    - Comfy dtype: `INT`
## Output types
- **`image`**
    - The extracted subset of images from the specified batch. This output is useful for further processing or analysis of specific images within a larger batch.
    - Python dtype: `torch.Tensor`
    - Comfy dtype: `IMAGE`
## Usage tips
- Infra type: `GPU`
- Common nodes: unknown


## Source code
```python
class ImageFromBatch:
    @classmethod
    def INPUT_TYPES(s):
        return {"required": { "image": ("IMAGE",),
                              "batch_index": ("INT", {"default": 0, "min": 0, "max": 63}),
                              "length": ("INT", {"default": 1, "min": 1, "max": 64}),
                              }}
    RETURN_TYPES = ("IMAGE",)
    FUNCTION = "frombatch"

    CATEGORY = "image/batch"

    def frombatch(self, image, batch_index, length):
        s_in = image
        batch_index = min(s_in.shape[0] - 1, batch_index)
        length = min(s_in.shape[0] - batch_index, length)
        s = s_in[batch_index:batch_index + length].clone()
        return (s,)

```
