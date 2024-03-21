# Convert Mask to Image
## Documentation
- Class name: `MaskToImage`
- Category: `mask`
- Output node: `False`

The `MaskToImage` node converts a mask into an image by reshaping and expanding the mask dimensions to match those of an image. This process involves adjusting the mask to have three color channels, effectively transforming it into a grayscale image where the mask's values are replicated across all three channels.
## Input types
### Required
- **`mask`**
    - The mask to be converted into an image. This mask serves as the basis for generating a grayscale image where the original mask values are duplicated across the RGB channels.
    - Python dtype: `torch.Tensor`
    - Comfy dtype: `MASK`
## Output types
- **`image`**
    - The resulting image obtained from the mask. This image is a grayscale representation where the mask's values are replicated across the RGB channels, allowing it to be used in contexts expecting an image format.
    - Python dtype: `torch.Tensor`
    - Comfy dtype: `IMAGE`
## Usage tips
- Infra type: `GPU`
- Common nodes: `PreviewImage,Paste By Mask,RegionalIPAdapterColorMask //Inspire,ImageCompositeMasked,ImageInvert,Image To Mask,Cut By Mask,Blur`


## Source code
```python
class MaskToImage:
    @classmethod
    def INPUT_TYPES(s):
        return {
                "required": {
                    "mask": ("MASK",),
                }
        }

    CATEGORY = "mask"

    RETURN_TYPES = ("IMAGE",)
    FUNCTION = "mask_to_image"

    def mask_to_image(self, mask):
        result = mask.reshape((-1, 1, mask.shape[-2], mask.shape[-1])).movedim(1, -1).expand(-1, -1, -1, 3)
        return (result,)

```
