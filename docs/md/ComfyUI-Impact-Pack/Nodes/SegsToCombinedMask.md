# SEGS to MASK (combined)
## Documentation
- Class name: `SegsToCombinedMask`
- Category: `ImpactPack/Operation`
- Output node: `False`

This node combines segmentation masks into a single mask. It iterates over each segmentation, applies a cropping based on the segmentation's region, and combines these cropped masks using a bitwise OR operation. The final mask is normalized to a range between 0 and 1.
## Input types
### Required
- **`segs`**
    - The input 'segs' represents a collection of segmentation masks along with their cropping regions. It is crucial for determining the spatial arrangement and combination of individual masks into a unified mask.
    - Python dtype: `Tuple[torch.Size, List[Segmentation]]`
    - Comfy dtype: `SEGS`
## Output types
- **`mask`**
    - The output is a single combined mask derived from the input segmentations. It is normalized to have values between 0 and 1, suitable for further processing or visualization.
    - Python dtype: `torch.Tensor`
    - Comfy dtype: `MASK`
## Usage tips
- Infra type: `GPU`
- Common nodes: `ImageCompositeMasked,Mask Gaussian Region`


## Source code
```python
class SegsToCombinedMask:
    @classmethod
    def INPUT_TYPES(s):
        return {"required": {"segs": ("SEGS",), }}

    RETURN_TYPES = ("MASK",)
    FUNCTION = "doit"

    CATEGORY = "ImpactPack/Operation"

    def doit(self, segs):
        mask = core.segs_to_combined_mask(segs)
        mask = utils.make_3d_mask(mask)
        return (mask,)

```
