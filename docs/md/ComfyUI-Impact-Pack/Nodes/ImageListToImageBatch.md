# Image List to Image Batch
## Documentation
- Class name: `ImageListToImageBatch`
- Category: `ImpactPack/Operation`
- Output node: `False`

This node transforms a list of images into a single batched image tensor. If the images have different sizes, they are upscaled to match the first image's size using the Lanczos resampling method before being concatenated along the batch dimension.
## Input types
### Required
- **`images`**
    - A list of image tensors to be batched together. The images are upscaled to match the size of the first image in the list if they differ in size, ensuring uniformity in the batch.
    - Python dtype: `List[torch.Tensor]`
    - Comfy dtype: `IMAGE`
## Output types
- **`image`**
    - A single tensor containing all input images batched along the first dimension. This tensor is suitable for batch processing in neural networks.
    - Python dtype: `torch.Tensor`
    - Comfy dtype: `IMAGE`
## Usage tips
- Infra type: `GPU`
- Common nodes: `VHS_VideoCombine,RIFE VFI,ImageUpscaleWithModel,Reroute,ImageSelector`


## Source code
```python
class ImageListToImageBatch:
    @classmethod
    def INPUT_TYPES(s):
        return {"required": {
                        "images": ("IMAGE", ),
                      }
                }

    INPUT_IS_LIST = True

    RETURN_TYPES = ("IMAGE", )
    FUNCTION = "doit"

    CATEGORY = "ImpactPack/Operation"

    def doit(self, images):
        if len(images) <= 1:
            return (images,)
        else:
            image1 = images[0]
            for image2 in images[1:]:
                if image1.shape[1:] != image2.shape[1:]:
                    image2 = comfy.utils.common_upscale(image2.movedim(-1, 1), image1.shape[2], image1.shape[1], "lanczos", "center").movedim(1, -1)
                image1 = torch.cat((image1, image2), dim=0)
            return (image1,)

```
