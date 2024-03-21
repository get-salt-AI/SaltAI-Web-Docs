# SEGS to Mask List
## Documentation
- Class name: `ImpactSEGSToMaskList`
- Category: `ImpactPack/Util`
- Output node: `False`

The `ImpactSEGSToMaskList` node transforms a collection of SEGS (segmentation data) into a list of masks. If the input SEGS collection is empty, it generates a single empty mask. Each mask in the resulting list is converted to a 3D mask format for further processing.
## Input types
### Required
- **`segs`**
    - The `segs` parameter represents the input segmentation data. It is crucial for the node's operation as it determines the content and size of the output mask list.
    - Python dtype: `List[torch.Tensor]`
    - Comfy dtype: `SEGS`
## Output types
- **`mask`**
    - The output is a list of masks derived from the input SEGS. Each mask is adjusted to a 3D format, suitable for further image processing tasks.
    - Python dtype: `List[torch.Tensor]`
    - Comfy dtype: `MASK`
## Usage tips
- Infra type: `CPU`
- Common nodes: unknown


## Source code
```python
class SEGSToMaskList:
    @classmethod
    def INPUT_TYPES(s):
        return {"required": {
                     "segs": ("SEGS", ),
                     },
                }

    RETURN_TYPES = ("MASK",)
    OUTPUT_IS_LIST = (True,)
    FUNCTION = "doit"

    CATEGORY = "ImpactPack/Util"

    def doit(self, segs):
        masks = core.segs_to_masklist(segs)
        if len(masks) == 0:
            empty_mask = torch.zeros(segs[0], dtype=torch.float32, device="cpu")
            masks = [empty_mask]
        masks = [utils.make_3d_mask(mask) for mask in masks]
        return (masks,)

```
