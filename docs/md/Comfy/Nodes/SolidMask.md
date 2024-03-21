# SolidMask
## Documentation
- Class name: `SolidMask`
- Category: `mask`
- Output node: `False`

The SolidMask node generates a solid mask with a uniform value across its dimensions. It creates a mask of specified width and height, filled with a given value.
## Input types
### Required
- **`value`**
    - Specifies the uniform value to fill the mask with. It determines the intensity or opacity of the mask.
    - Python dtype: `float`
    - Comfy dtype: `FLOAT`
- **`width`**
    - Determines the width of the generated mask. It specifies how wide the mask will be.
    - Python dtype: `int`
    - Comfy dtype: `INT`
- **`height`**
    - Determines the height of the generated mask. It specifies how tall the mask will be.
    - Python dtype: `int`
    - Comfy dtype: `INT`
## Output types
- **`mask`**
    - The output is a solid mask with uniform value, specified width, and height.
    - Python dtype: `torch.Tensor`
    - Comfy dtype: `MASK`
## Usage tips
- Infra type: `CPU`
- Common nodes: `MaskComposite,MaskToImage,FeatherMask`


## Source code
```python
class SolidMask:
    @classmethod
    def INPUT_TYPES(cls):
        return {
            "required": {
                "value": ("FLOAT", {"default": 1.0, "min": 0.0, "max": 1.0, "step": 0.01}),
                "width": ("INT", {"default": 512, "min": 1, "max": MAX_RESOLUTION, "step": 1}),
                "height": ("INT", {"default": 512, "min": 1, "max": MAX_RESOLUTION, "step": 1}),
            }
        }

    CATEGORY = "mask"

    RETURN_TYPES = ("MASK",)

    FUNCTION = "solid"

    def solid(self, value, width, height):
        out = torch.full((1, height, width), value, dtype=torch.float32, device="cpu")
        return (out,)

```
