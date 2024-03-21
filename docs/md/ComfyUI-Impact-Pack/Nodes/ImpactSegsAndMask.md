# Bitwise(SEGS & MASK)
## Documentation
- Class name: `ImpactSegsAndMask`
- Category: `ImpactPack/Operation`
- Output node: `False`

The `ImpactSegsAndMask` node applies a bitwise AND operation between the mask of each segment in the input 'segs' and a given 'mask', producing a new set of segments where each segment's mask is the result of this operation. This is useful for refining segment masks based on an external mask, such as removing parts of segments that fall outside a region of interest.
## Input types
### Required
- **`segs`**
    - The input 'segs' represents the segments to be processed. Each segment includes a mask that will be combined with the given 'mask' using a bitwise AND operation. This input is crucial for determining which parts of the segments are kept based on the external mask.
    - Python dtype: `Tuple[torch.Tensor, List[SEG]]`
    - Comfy dtype: `SEGS`
- **`mask`**
    - The 'mask' input is an external mask that will be applied to each segment's mask in the 'segs' input using a bitwise AND operation. This allows for refining the segments based on areas defined by the 'mask'.
    - Python dtype: `torch.Tensor`
    - Comfy dtype: `MASK`
## Output types
- **`segs`**
    - The output 'segs' is a tuple containing the original shape information and a list of updated segments, where each segment's mask has been refined by applying the external 'mask' through a bitwise AND operation.
    - Python dtype: `Tuple[torch.Tensor, List[SEG]]`
    - Comfy dtype: `SEGS`
## Usage tips
- Infra type: `CPU`
- Common nodes: `ImpactSEGSOrderedFilter`


## Source code
```python
class SegsBitwiseAndMask:
    @classmethod
    def INPUT_TYPES(s):
        return {"required": {
                        "segs": ("SEGS",),
                        "mask": ("MASK",),
                    }
                }

    RETURN_TYPES = ("SEGS",)
    FUNCTION = "doit"

    CATEGORY = "ImpactPack/Operation"

    def doit(self, segs, mask):
        return (core.segs_bitwise_and_mask(segs, mask), )

```
