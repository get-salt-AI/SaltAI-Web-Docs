# RepeatImageBatch
## Documentation
- Class name: `RepeatImageBatch`
- Category: `image/batch`
- Output node: `False`

This node repeats an input image a specified number of times along the batch dimension, effectively creating a batch of identical images. This can be useful for operations that require batch processing of the same image.
## Input types
### Required
- **`image`**
    - The image to be repeated. This parameter is crucial for defining the input that will be processed multiple times in a batch.
    - Python dtype: `torch.Tensor`
    - Comfy dtype: `IMAGE`
- **`amount`**
    - Specifies how many times the input image should be repeated. This affects the size of the output batch, allowing for batch processing of a single image.
    - Python dtype: `int`
    - Comfy dtype: `INT`
## Output types
- **`image`**
    - A batch of images, where each image in the batch is an identical copy of the input image.
    - Python dtype: `torch.Tensor`
    - Comfy dtype: `IMAGE`
## Usage tips
- Infra type: `GPU`
- Common nodes: unknown


## Source code
```python
class RepeatImageBatch:
    @classmethod
    def INPUT_TYPES(s):
        return {"required": { "image": ("IMAGE",),
                              "amount": ("INT", {"default": 1, "min": 1, "max": 64}),
                              }}
    RETURN_TYPES = ("IMAGE",)
    FUNCTION = "repeat"

    CATEGORY = "image/batch"

    def repeat(self, image, amount):
        s = image.repeat((amount, 1,1,1))
        return (s,)

```
