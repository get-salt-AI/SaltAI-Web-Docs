# ToBinaryMask
## Documentation
- Class name: `ToBinaryMask`
- Category: `ImpactPack/Operation`
- Output node: `False`

This node converts a given mask to a binary mask based on a specified threshold. It is a part of the ImpactPack/Operation category and is used to simplify mask data by categorizing pixel values as either 0 or 1, depending on whether they fall below or above the threshold.
## Input types
### Required
- **`mask`**
    - The mask input is the primary data that will be processed to generate a binary mask. The threshold parameter will determine how the values in this mask are categorized.
    - Python dtype: `torch.Tensor`
    - Comfy dtype: `MASK`
- **`threshold`**
    - The threshold parameter determines the cutoff value for converting the mask to binary. Pixels with values above this threshold will be considered as part of the mask (1), and those below will be considered as background (0).
    - Python dtype: `int`
    - Comfy dtype: `INT`
## Output types
- **`mask`**
    - The output is a binary mask where each pixel is either 0 or 1, indicating absence or presence in the mask, respectively.
    - Python dtype: `torch.Tensor`
    - Comfy dtype: `MASK`
## Usage tips
- Infra type: `CPU`
- Common nodes: `MaskToImage,MaskToSEGS`


## Source code
```python
class ToBinaryMask:
    @classmethod
    def INPUT_TYPES(s):
        return {"required": {
                      "mask": ("MASK",),
                      "threshold": ("INT", {"default": 20, "min": 1, "max": 255}),
                    }
                }

    RETURN_TYPES = ("MASK",)
    FUNCTION = "doit"

    CATEGORY = "ImpactPack/Operation"

    def doit(self, mask, threshold):
        mask = to_binary_mask(mask, threshold/255.0)
        return (mask,)

```
