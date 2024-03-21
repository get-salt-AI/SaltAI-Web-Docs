# GrowMask
## Documentation
- Class name: `GrowMask`
- Category: `mask`
- Output node: `False`

The `GrowMask` node is designed to modify the size of a given mask by either expanding or contracting it, with an option to taper the corners for a smoother transition. This operation is useful for adjusting mask boundaries in image processing tasks.
## Input types
### Required
- **`mask`**
    - The input mask to be modified. It serves as the base for the expansion or contraction operation.
    - Python dtype: `torch.Tensor`
    - Comfy dtype: `MASK`
- **`expand`**
    - Determines the number of pixels by which the mask will be expanded (positive value) or contracted (negative value).
    - Python dtype: `int`
    - Comfy dtype: `INT`
- **`tapered_corners`**
    - A boolean flag that, when set to True, applies a tapering effect to the corners of the mask during the expansion or contraction process, creating a smoother transition.
    - Python dtype: `bool`
    - Comfy dtype: `BOOLEAN`
## Output types
- **`mask`**
    - The modified mask after the expansion or contraction operation, potentially with tapered corners if specified.
    - Python dtype: `torch.Tensor`
    - Comfy dtype: `MASK`
## Usage tips
- Infra type: `CPU`
- Common nodes: `MaskBlur+,ImageCompositeMasked,MaskToImage,VAEEncodeForInpaint`


## Source code
```python
class GrowMask:
    @classmethod
    def INPUT_TYPES(cls):
        return {
            "required": {
                "mask": ("MASK",),
                "expand": ("INT", {"default": 0, "min": -MAX_RESOLUTION, "max": MAX_RESOLUTION, "step": 1}),
                "tapered_corners": ("BOOLEAN", {"default": True}),
            },
        }
    
    CATEGORY = "mask"

    RETURN_TYPES = ("MASK",)

    FUNCTION = "expand_mask"

    def expand_mask(self, mask, expand, tapered_corners):
        c = 0 if tapered_corners else 1
        kernel = np.array([[c, 1, c],
                           [1, 1, 1],
                           [c, 1, c]])
        mask = mask.reshape((-1, mask.shape[-2], mask.shape[-1]))
        out = []
        for m in mask:
            output = m.numpy()
            for _ in range(abs(expand)):
                if expand < 0:
                    output = scipy.ndimage.grey_erosion(output, footprint=kernel)
                else:
                    output = scipy.ndimage.grey_dilation(output, footprint=kernel)
            output = torch.from_numpy(output)
            out.append(output)
        return (torch.stack(out, dim=0),)

```
