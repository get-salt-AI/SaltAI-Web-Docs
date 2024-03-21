# Masks to Mask List
## Documentation
- Class name: `MasksToMaskList`
- Category: `ImpactPack/Operation`
- Output node: `False`

Converts a list of masks into a list of 3D masks, handling cases where the input is empty by providing a default empty mask. This operation is useful for standardizing mask formats for further processing.
## Input types
### Required
- **`masks`**
    - The input masks to be converted. If `None`, a default empty mask is generated. This parameter is crucial for the conversion process, affecting the output by either passing through the input masks or generating a default mask in case of `None`.
    - Python dtype: `Optional[List[torch.Tensor]]`
    - Comfy dtype: `MASK`
## Output types
- **`mask`**
    - A list of 3D masks, either directly converted from the input or containing a single default empty mask if the input was `None`.
    - Python dtype: `List[torch.Tensor]`
    - Comfy dtype: `MASK`
## Usage tips
- Infra type: `CPU`
- Common nodes: unknown


## Source code
```python
class MasksToMaskList:
    @classmethod
    def INPUT_TYPES(s):
        return {"required": {
                        "masks": ("MASK", ),
                      }
                }

    RETURN_TYPES = ("MASK", )
    OUTPUT_IS_LIST = (True, )
    FUNCTION = "doit"

    CATEGORY = "ImpactPack/Operation"

    def doit(self, masks):
        if masks is None:
            empty_mask = torch.zeros((64, 64), dtype=torch.float32, device="cpu")
            return ([empty_mask], )

        res = []

        for mask in masks:
            res.append(mask)

        print(f"mask len: {len(res)}")

        res = [make_3d_mask(x) for x in res]

        return (res, )

```
