# ImageCompositeMasked
## Documentation
- Class name: `ImageCompositeMasked`
- Category: `image`
- Output node: `False`

The `ImageCompositeMasked` node is designed for compositing two images together at specified coordinates, optionally resizing the source image and applying a mask to control the blending regions. This operation is fundamental in image editing and processing tasks where merging different image layers or elements is required.
## Input types
### Required
- **`destination`**
    - The base image onto which the source image will be composited. It serves as the background layer in the compositing process.
    - Python dtype: `torch.Tensor`
    - Comfy dtype: `IMAGE`
- **`source`**
    - The image to be composited onto the destination image. It acts as the foreground layer and can be optionally resized to fit the destination image's context.
    - Python dtype: `torch.Tensor`
    - Comfy dtype: `IMAGE`
- **`x`**
    - The x-coordinate on the destination image where the top-left corner of the source image will be placed. It determines the horizontal positioning of the source image.
    - Python dtype: `int`
    - Comfy dtype: `INT`
- **`y`**
    - The y-coordinate on the destination image where the top-left corner of the source image will be placed. It determines the vertical positioning of the source image.
    - Python dtype: `int`
    - Comfy dtype: `INT`
- **`resize_source`**
    - A boolean flag indicating whether the source image should be resized to match the destination image's dimensions. This allows for flexible compositing of images of different sizes.
    - Python dtype: `bool`
    - Comfy dtype: `BOOLEAN`
### Optional
- **`mask`**
    - An optional mask that defines which parts of the source image should be visible when composited onto the destination image. This allows for selective blending and more complex compositing effects.
    - Python dtype: `Optional[torch.Tensor]`
    - Comfy dtype: `MASK`
## Output types
- **`image`**
    - The resulting image after compositing the source image onto the destination image, with optional resizing and masking applied.
    - Python dtype: `torch.Tensor`
    - Comfy dtype: `IMAGE`
## Usage tips
- Infra type: `GPU`
- Common nodes: `ImageInvert,PreviewImage,ImageCompositeMasked,ControlNetApplyAdvanced,VAEEncode,ColorCorrect`


## Source code
```python
class ImageCompositeMasked:
    @classmethod
    def INPUT_TYPES(s):
        return {
            "required": {
                "destination": ("IMAGE",),
                "source": ("IMAGE",),
                "x": ("INT", {"default": 0, "min": 0, "max": MAX_RESOLUTION, "step": 1}),
                "y": ("INT", {"default": 0, "min": 0, "max": MAX_RESOLUTION, "step": 1}),
                "resize_source": ("BOOLEAN", {"default": False}),
            },
            "optional": {
                "mask": ("MASK",),
            }
        }
    RETURN_TYPES = ("IMAGE",)
    FUNCTION = "composite"

    CATEGORY = "image"

    def composite(self, destination, source, x, y, resize_source, mask = None):
        destination = destination.clone().movedim(-1, 1)
        output = composite(destination, source.movedim(-1, 1), x, y, mask, 1, resize_source).movedim(1, -1)
        return (output,)

```
