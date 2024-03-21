# FeatherMask
## Documentation
- Class name: `FeatherMask`
- Category: `mask`
- Output node: `False`

The `FeatherMask` node applies a feathering effect to the edges of a given mask. This effect gradually fades the mask's edges by adjusting their opacity, creating a smoother transition at the specified boundaries.
## Input types
### Required
- **`mask`**
    - The mask to which the feathering effect will be applied. It's crucial for creating smooth transitions at the mask's edges.
    - Python dtype: `torch.Tensor`
    - Comfy dtype: `MASK`
- **`left`**
    - Specifies the width of the feathering effect on the left edge of the mask.
    - Python dtype: `int`
    - Comfy dtype: `INT`
- **`top`**
    - Specifies the height of the feathering effect on the top edge of the mask.
    - Python dtype: `int`
    - Comfy dtype: `INT`
- **`right`**
    - Specifies the width of the feathering effect on the right edge of the mask.
    - Python dtype: `int`
    - Comfy dtype: `INT`
- **`bottom`**
    - Specifies the height of the feathering effect on the bottom edge of the mask.
    - Python dtype: `int`
    - Comfy dtype: `INT`
## Output types
- **`mask`**
    - The modified mask with a feathered edge effect applied to its specified boundaries.
    - Python dtype: `torch.Tensor`
    - Comfy dtype: `MASK`
## Usage tips
- Infra type: `CPU`
- Common nodes: `MaskComposite,Reroute`


## Source code
```python
class FeatherMask:
    @classmethod
    def INPUT_TYPES(cls):
        return {
            "required": {
                "mask": ("MASK",),
                "left": ("INT", {"default": 0, "min": 0, "max": MAX_RESOLUTION, "step": 1}),
                "top": ("INT", {"default": 0, "min": 0, "max": MAX_RESOLUTION, "step": 1}),
                "right": ("INT", {"default": 0, "min": 0, "max": MAX_RESOLUTION, "step": 1}),
                "bottom": ("INT", {"default": 0, "min": 0, "max": MAX_RESOLUTION, "step": 1}),
            }
        }

    CATEGORY = "mask"

    RETURN_TYPES = ("MASK",)

    FUNCTION = "feather"

    def feather(self, mask, left, top, right, bottom):
        output = mask.reshape((-1, mask.shape[-2], mask.shape[-1])).clone()

        left = min(left, output.shape[-1])
        right = min(right, output.shape[-1])
        top = min(top, output.shape[-2])
        bottom = min(bottom, output.shape[-2])

        for x in range(left):
            feather_rate = (x + 1.0) / left
            output[:, :, x] *= feather_rate

        for x in range(right):
            feather_rate = (x + 1) / right
            output[:, :, -x] *= feather_rate

        for y in range(top):
            feather_rate = (y + 1) / top
            output[:, y, :] *= feather_rate

        for y in range(bottom):
            feather_rate = (y + 1) / bottom
            output[:, -y, :] *= feather_rate

        return (output,)

```
