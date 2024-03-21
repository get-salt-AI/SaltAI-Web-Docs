# MaskComposite
## Documentation
- Class name: `MaskComposite`
- Category: `mask`
- Output node: `False`

This node is designed to perform operations on masks, such as combining them through mathematical or logical operations. It allows for the manipulation of mask data to achieve desired effects or transformations.
## Input types
### Required
- **`destination`**
    - The primary mask onto which the operation will be applied. It serves as the base for the composite operation.
    - Python dtype: `torch.Tensor`
    - Comfy dtype: `MASK`
- **`source`**
    - The secondary mask that will be used in the operation with the destination mask. It can be combined in various ways depending on the specified operation.
    - Python dtype: `torch.Tensor`
    - Comfy dtype: `MASK`
- **`x`**
    - The horizontal offset at which the source mask will be applied to the destination mask.
    - Python dtype: `int`
    - Comfy dtype: `INT`
- **`y`**
    - The vertical offset at which the source mask will be applied to the destination mask.
    - Python dtype: `int`
    - Comfy dtype: `INT`
- **`operation`**
    - Specifies the type of operation to be performed when combining the destination and source masks. Options include mathematical and logical operations like multiply, add, subtract, and, or, xor.
    - Python dtype: `str`
    - Comfy dtype: `['multiply', 'add', 'subtract', 'and', 'or', 'xor']`
## Output types
- **`mask`**
    - The result of the composite operation applied to the masks, producing a new mask.
    - Python dtype: `torch.Tensor`
    - Comfy dtype: `MASK`
## Usage tips
- Infra type: `CPU`
- Common nodes: `ToBinaryMask,LatentCompositeMasked,MaskToImage`


## Source code
```python
class MaskComposite:
    @classmethod
    def INPUT_TYPES(cls):
        return {
            "required": {
                "destination": ("MASK",),
                "source": ("MASK",),
                "x": ("INT", {"default": 0, "min": 0, "max": MAX_RESOLUTION, "step": 1}),
                "y": ("INT", {"default": 0, "min": 0, "max": MAX_RESOLUTION, "step": 1}),
                "operation": (["multiply", "add", "subtract", "and", "or", "xor"],),
            }
        }

    CATEGORY = "mask"

    RETURN_TYPES = ("MASK",)

    FUNCTION = "combine"

    def combine(self, destination, source, x, y, operation):
        output = destination.reshape((-1, destination.shape[-2], destination.shape[-1])).clone()
        source = source.reshape((-1, source.shape[-2], source.shape[-1]))

        left, top = (x, y,)
        right, bottom = (min(left + source.shape[-1], destination.shape[-1]), min(top + source.shape[-2], destination.shape[-2]))
        visible_width, visible_height = (right - left, bottom - top,)

        source_portion = source[:, :visible_height, :visible_width]
        destination_portion = destination[:, top:bottom, left:right]

        if operation == "multiply":
            output[:, top:bottom, left:right] = destination_portion * source_portion
        elif operation == "add":
            output[:, top:bottom, left:right] = destination_portion + source_portion
        elif operation == "subtract":
            output[:, top:bottom, left:right] = destination_portion - source_portion
        elif operation == "and":
            output[:, top:bottom, left:right] = torch.bitwise_and(destination_portion.round().bool(), source_portion.round().bool()).float()
        elif operation == "or":
            output[:, top:bottom, left:right] = torch.bitwise_or(destination_portion.round().bool(), source_portion.round().bool()).float()
        elif operation == "xor":
            output[:, top:bottom, left:right] = torch.bitwise_xor(destination_portion.round().bool(), source_portion.round().bool()).float()

        output = torch.clamp(output, 0.0, 1.0)

        return (output,)

```
