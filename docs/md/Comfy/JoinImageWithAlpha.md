# Join Image with Alpha
## Documentation
- Class name: `JoinImageWithAlpha`
- Category: `mask/compositing`
- Output node: `False`

This node combines an image and an alpha mask to produce a single image with transparency information. It adjusts the alpha mask to match the image dimensions and merges them, ensuring the output image retains the original colors with the specified transparency.
## Input types
### Required
- **`image`**
    - The primary image to which the alpha mask will be applied. It defines the visual content of the output.
    - Python dtype: `torch.Tensor`
    - Comfy dtype: `IMAGE`
- **`alpha`**
    - The alpha mask that specifies the transparency level for each pixel in the image. It is adjusted to match the image dimensions before application.
    - Python dtype: `torch.Tensor`
    - Comfy dtype: `MASK`
## Output types
- **`image`**
    - The output is a single image that combines the input image with the alpha mask, incorporating transparency information.
    - Python dtype: `Tuple[torch.Tensor]`
    - Comfy dtype: `IMAGE`
## Usage tips
- Infra type: `GPU`
- Common nodes: unknown


## Source code
```python
class JoinImageWithAlpha:
    @classmethod
    def INPUT_TYPES(s):
        return {
                "required": {
                    "image": ("IMAGE",),
                    "alpha": ("MASK",),
                }
        }

    CATEGORY = "mask/compositing"
    RETURN_TYPES = ("IMAGE",)
    FUNCTION = "join_image_with_alpha"

    def join_image_with_alpha(self, image: torch.Tensor, alpha: torch.Tensor):
        batch_size = min(len(image), len(alpha))
        out_images = []

        alpha = 1.0 - resize_mask(alpha, image.shape[1:])
        for i in range(batch_size):
           out_images.append(torch.cat((image[i][:,:,:3], alpha[i].unsqueeze(2)), dim=2))

        result = (torch.stack(out_images),)
        return result

```
