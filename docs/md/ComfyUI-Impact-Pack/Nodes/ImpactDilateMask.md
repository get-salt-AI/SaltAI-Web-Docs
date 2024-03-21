# Dilate Mask
## Documentation
- Class name: `ImpactDilateMask`
- Category: `ImpactPack/Util`
- Output node: `False`

The `ImpactDilateMask` node applies dilation or erosion to a given mask based on the dilation factor. If the dilation factor is positive, it dilates the mask, increasing the area of the foreground regions. If the factor is negative, it performs erosion, reducing the foreground areas. This operation is useful for adjusting the boundaries of mask regions in image processing tasks.
## Input types
### Required
- **`mask`**
    - The input mask to be dilated or eroded. This mask represents the areas of interest in an image, and modifying its boundaries can be crucial for subsequent image analysis or processing steps.
    - Python dtype: `torch.Tensor`
    - Comfy dtype: `MASK`
- **`dilation`**
    - The factor by which the mask is dilated or eroded. A positive value causes dilation, expanding the mask areas, while a negative value causes erosion, shrinking them. This allows for flexible adjustments to the mask's boundaries.
    - Python dtype: `int`
    - Comfy dtype: `INT`
## Output types
- **`mask`**
    - The modified mask after applying dilation or erosion. This output is crucial for further processing or analysis where the precise definition of object boundaries in the mask is important.
    - Python dtype: `torch.Tensor`
    - Comfy dtype: `MASK`
## Usage tips
- Infra type: `GPU`
- Common nodes: `Mask Threshold Region`


## Source code
```python
class DilateMask:
    @classmethod
    def INPUT_TYPES(s):
        return {"required": {
                     "mask": ("MASK", ),
                     "dilation": ("INT", {"default": 10, "min": -512, "max": 512, "step": 1}),
                }}

    RETURN_TYPES = ("MASK", )

    FUNCTION = "doit"

    CATEGORY = "ImpactPack/Util"

    def doit(self, mask, dilation):
        mask = core.dilate_mask(mask.numpy(), dilation)
        mask = torch.from_numpy(mask)
        mask = utils.make_3d_mask(mask)
        return (mask, )

```
