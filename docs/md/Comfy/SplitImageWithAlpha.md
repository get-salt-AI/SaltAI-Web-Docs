# Split Image with Alpha
## Documentation
- Class name: `SplitImageWithAlpha`
- Category: `mask/compositing`
- Output node: `False`

The `SplitImageWithAlpha` node is designed to separate an image into its color components and its alpha (transparency) channel. It processes each image in a batch, extracting the RGB channels as output images and computing the alpha channel, which is then inverted to represent transparency.
## Input types
### Required
- **`image`**
    - The input image tensor, expected to contain an alpha channel alongside the RGB channels. This tensor is split into RGB components and the alpha channel, facilitating operations that require separate handling of image content and transparency.
    - Python dtype: `torch.Tensor`
    - Comfy dtype: `IMAGE`
## Output types
- **`image`**
    - The RGB components of the input images, extracted as a separate tensor.
    - Python dtype: `torch.Tensor`
    - Comfy dtype: `IMAGE`
- **`mask`**
    - The inverted alpha channel of the input images, representing transparency.
    - Python dtype: `torch.Tensor`
    - Comfy dtype: `MASK`
## Usage tips
- Infra type: `GPU`
- Common nodes: `PorterDuffImageComposite`


## Source code
```python
class SplitImageWithAlpha:
    @classmethod
    def INPUT_TYPES(s):
        return {
                "required": {
                    "image": ("IMAGE",),
                }
        }

    CATEGORY = "mask/compositing"
    RETURN_TYPES = ("IMAGE", "MASK")
    FUNCTION = "split_image_with_alpha"

    def split_image_with_alpha(self, image: torch.Tensor):
        out_images = [i[:,:,:3] for i in image]
        out_alphas = [i[:,:,3] if i.shape[2] > 3 else torch.ones_like(i[:,:,0]) for i in image]
        result = (torch.stack(out_images), 1.0 - torch.stack(out_alphas))
        return result

```
