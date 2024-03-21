# Pad Image for Outpainting
## Documentation
- Class name: `ImagePadForOutpaint`
- Category: `image`
- Output node: `False`

The ImagePadForOutpaint node is designed to prepare images for the outpainting process by padding them. This operation is crucial for expanding the canvas of an image, allowing for the generation or extension of image content beyond its original borders.
## Input types
### Required
- **`image`**
    - The original image to be padded for outpainting. This image serves as the base for the outpainting process, where additional content will be generated around its borders.
    - Python dtype: `torch.Tensor`
    - Comfy dtype: `IMAGE`
- **`left`**
    - Specifies the amount of padding to be applied to the left side of the image, influencing the extent of the outpainting area.
    - Python dtype: `int`
    - Comfy dtype: `INT`
- **`top`**
    - Specifies the amount of padding to be applied to the top side of the image, influencing the extent of the outpainting area.
    - Python dtype: `int`
    - Comfy dtype: `INT`
- **`right`**
    - Specifies the amount of padding to be applied to the right side of the image, influencing the extent of the outpainting area.
    - Python dtype: `int`
    - Comfy dtype: `INT`
- **`bottom`**
    - Specifies the amount of padding to be applied to the bottom side of the image, influencing the extent of the outpainting area.
    - Python dtype: `int`
    - Comfy dtype: `INT`
- **`feathering`**
    - Determines the smoothness of the transition between the original image and the padded area, enhancing the visual integration of the outpainting.
    - Python dtype: `int`
    - Comfy dtype: `INT`
## Output types
- **`image`**
    - The resulting image after padding has been applied. This image has an expanded canvas ready for outpainting.
    - Python dtype: `torch.Tensor`
    - Comfy dtype: `IMAGE`
- **`mask`**
    - The mask indicating the original and padded areas, useful for distinguishing between them in subsequent processing steps.
    - Python dtype: `torch.Tensor`
    - Comfy dtype: `MASK`
## Usage tips
- Infra type: `GPU`
- Common nodes: unknown


## Source code
```python
class ImagePadForOutpaint:

    @classmethod
    def INPUT_TYPES(s):
        return {
            "required": {
                "image": ("IMAGE",),
                "left": ("INT", {"default": 0, "min": 0, "max": MAX_RESOLUTION, "step": 8}),
                "top": ("INT", {"default": 0, "min": 0, "max": MAX_RESOLUTION, "step": 8}),
                "right": ("INT", {"default": 0, "min": 0, "max": MAX_RESOLUTION, "step": 8}),
                "bottom": ("INT", {"default": 0, "min": 0, "max": MAX_RESOLUTION, "step": 8}),
                "feathering": ("INT", {"default": 40, "min": 0, "max": MAX_RESOLUTION, "step": 1}),
            }
        }

    RETURN_TYPES = ("IMAGE", "MASK")
    FUNCTION = "expand_image"

    CATEGORY = "image"

    def expand_image(self, image, left, top, right, bottom, feathering):
        d1, d2, d3, d4 = image.size()

        new_image = torch.ones(
            (d1, d2 + top + bottom, d3 + left + right, d4),
            dtype=torch.float32,
        ) * 0.5

        new_image[:, top:top + d2, left:left + d3, :] = image

        mask = torch.ones(
            (d2 + top + bottom, d3 + left + right),
            dtype=torch.float32,
        )

        t = torch.zeros(
            (d2, d3),
            dtype=torch.float32
        )

        if feathering > 0 and feathering * 2 < d2 and feathering * 2 < d3:

            for i in range(d2):
                for j in range(d3):
                    dt = i if top != 0 else d2
                    db = d2 - i if bottom != 0 else d2

                    dl = j if left != 0 else d3
                    dr = d3 - j if right != 0 else d3

                    d = min(dt, db, dl, dr)

                    if d >= feathering:
                        continue

                    v = (feathering - d) / feathering

                    t[i, j] = v * v

        mask[top:top + d2, left:left + d3] = t

        return (new_image, mask)

```
