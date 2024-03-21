# SEGS to Mask Batch
## Documentation
- Class name: `ImpactSEGSToMaskBatch`
- Category: `ImpactPack/Util`
- Output node: `False`

The `ImpactSEGSToMaskBatch` node is designed to convert a collection of segmentation data (SEGS) into a batch of masks. It first utilizes a core function to transform the segmentation data into a list of individual masks. These masks are then processed to ensure they are in a 3D format suitable for batch processing. The final step involves concatenating these 3D masks into a single tensor, creating a batch of masks that can be used for further processing or analysis.
## Input types
### Required
- **`segs`**
    - The `segs` parameter represents the input segmentation data that is to be converted into a batch of masks. This data is crucial for the operation as it directly influences the output masks' quality and characteristics.
    - Python dtype: `List[torch.Tensor]`
    - Comfy dtype: `SEGS`
## Output types
- **`mask`**
    - The output is a batch of masks, represented as a single tensor, where each mask corresponds to the input segmentation data. This batch can be utilized for various tasks, including training machine learning models or performing image analysis.
    - Python dtype: `torch.Tensor`
    - Comfy dtype: `MASK`
## Usage tips
- Infra type: `GPU`
- Common nodes: unknown


## Source code
```python
class SEGSToMaskBatch:
    @classmethod
    def INPUT_TYPES(s):
        return {"required": {
                     "segs": ("SEGS", ),
                     },
                }

    RETURN_TYPES = ("MASK",)
    FUNCTION = "doit"

    CATEGORY = "ImpactPack/Util"

    def doit(self, segs):
        masks = core.segs_to_masklist(segs)
        masks = [utils.make_3d_mask(mask) for mask in masks]
        mask_batch = torch.concat(masks)
        return (mask_batch,)

```
