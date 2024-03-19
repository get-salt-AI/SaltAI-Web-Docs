# Convert Image to Mask
## Documentation
- Class name: `ImageToMask`
- Category: `mask`
- Output node: `False`

This node converts an image into a mask based on a specified color channel (red, green, blue, or alpha). It isolates the selected channel from the input image to create a mask, which can be used for various image processing tasks that require a binary or grayscale representation of the image.
## Input types
### Required
- **`image`**
    - The input image from which the mask will be created. The choice of channel affects the resulting mask, highlighting different features based on the channel's color spectrum.
    - Python dtype: `torch.Tensor`
    - Comfy dtype: `IMAGE`
- **`channel`**
    - Specifies the color channel (red, green, blue, or alpha) to be used for mask creation. This choice determines which aspects of the image are emphasized in the resulting mask.
    - Python dtype: `str`
    - Comfy dtype: `STRING`
## Output types
- **`mask`**
    - The output mask generated from the specified color channel of the input image. This mask can be used for further image manipulation or analysis.
    - Python dtype: `torch.Tensor`
    - Comfy dtype: `MASK`
## Usage tips
- Infra type: `GPU`
- Common nodes: `MaskComposite,GrowMask,IPAdapterApplyFaceID`


## Source code
```python
class ImageToMask:
    @classmethod
    def INPUT_TYPES(s):
        return {
                "required": {
                    "image": ("IMAGE",),
                    "channel": (["red", "green", "blue", "alpha"],),
                }
        }

    CATEGORY = "mask"

    RETURN_TYPES = ("MASK",)
    FUNCTION = "image_to_mask"

    def image_to_mask(self, image, channel):
        channels = ["red", "green", "blue", "alpha"]
        mask = image[:, :, :, channels.index(channel)]
        return (mask,)

```
