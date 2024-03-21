# Mask List to Masks
## Documentation
- Class name: `MaskListToMaskBatch`
- Category: `ImpactPack/Operation`
- Output node: `False`

This node transforms a list of masks into a batch of masks, handling different scenarios such as a single mask, multiple masks with potentially different dimensions, or no masks at all. It ensures that all masks in the batch have consistent dimensions, either by upsampling smaller masks or creating an empty mask if no input is provided.
## Input types
### Required
- **`mask`**
    - The input mask or list of masks to be transformed into a batch. This parameter is crucial for determining the output shape and content of the mask batch, affecting the node's execution by handling different scenarios like single or multiple masks.
    - Python dtype: `List[torch.Tensor]`
    - Comfy dtype: `MASK`
## Output types
- **`mask`**
    - The output is a batch of masks, which could be a single mask, a concatenated batch of multiple masks, or an empty mask, depending on the input.
    - Python dtype: `torch.Tensor`
    - Comfy dtype: `MASK`
## Usage tips
- Infra type: `GPU`
- Common nodes: unknown


## Source code
```python
class MaskListToMaskBatch:
    @classmethod
    def INPUT_TYPES(s):
        return {"required": {
                        "mask": ("MASK", ),
                      }
                }

    INPUT_IS_LIST = True

    RETURN_TYPES = ("MASK", )
    FUNCTION = "doit"

    CATEGORY = "ImpactPack/Operation"

    def doit(self, mask):
        if len(mask) == 1:
            mask = make_3d_mask(mask[0])
            return (mask,)
        elif len(mask) > 1:
            mask1 = make_3d_mask(mask[0])

            for mask2 in mask[1:]:
                mask2 = make_3d_mask(mask2)
                if mask1.shape[1:] != mask2.shape[1:]:
                    mask2 = comfy.utils.common_upscale(mask2.movedim(-1, 1), mask1.shape[2], mask1.shape[1], "lanczos", "center").movedim(1, -1)
                mask1 = torch.cat((mask1, mask2), dim=0)

            return (mask1,)
        else:
            empty_mask = torch.zeros((1, 64, 64), dtype=torch.float32, device="cpu").unsqueeze(0)
            return (empty_mask,)

```
