# Switch (images, mask)
## Documentation
- Class name: `ImageMaskSwitch`
- Category: `ImpactPack/Util`
- Output node: `True`

The `ImageMaskSwitch` node allows for the selection and output of a specific image and its associated optional mask from a set of up to four images and their corresponding masks, based on a selection index.
## Input types
### Required
- **`select`**
    - Determines which image and its corresponding mask (if provided) are selected for output. The selection index ranges from 1 to 4.
    - Python dtype: `int`
    - Comfy dtype: `INT`
- **`images1`**
    - The first image in the selection set. It is always required.
    - Python dtype: `torch.Tensor`
    - Comfy dtype: `IMAGE`
### Optional
- **`mask1_opt`**
    - An optional mask associated with the first image.
    - Python dtype: `Optional[torch.Tensor]`
    - Comfy dtype: `MASK`
- **`images2_opt`**
    - The second optional image in the selection set.
    - Python dtype: `Optional[torch.Tensor]`
    - Comfy dtype: `IMAGE`
- **`mask2_opt`**
    - An optional mask associated with the second image.
    - Python dtype: `Optional[torch.Tensor]`
    - Comfy dtype: `MASK`
- **`images3_opt`**
    - The third optional image in the selection set.
    - Python dtype: `Optional[torch.Tensor]`
    - Comfy dtype: `IMAGE`
- **`mask3_opt`**
    - An optional mask associated with the third image.
    - Python dtype: `Optional[torch.Tensor]`
    - Comfy dtype: `MASK`
- **`images4_opt`**
    - The fourth optional image in the selection set.
    - Python dtype: `Optional[torch.Tensor]`
    - Comfy dtype: `IMAGE`
- **`mask4_opt`**
    - An optional mask associated with the fourth image.
    - Python dtype: `Optional[torch.Tensor]`
    - Comfy dtype: `MASK`
## Output types
- **`image`**
    - The selected image based on the `select` index.
    - Python dtype: `torch.Tensor`
    - Comfy dtype: `IMAGE`
- **`mask`**
    - The optional mask associated with the selected image, if provided.
    - Python dtype: `Optional[torch.Tensor]`
    - Comfy dtype: `MASK`
## Usage tips
- Infra type: `CPU`
- Common nodes: `Reroute`


## Source code
```python
class ImageMaskSwitch:
    @classmethod
    def INPUT_TYPES(s):
        return {"required": {
            "select": ("INT", {"default": 1, "min": 1, "max": 4, "step": 1}),
            "images1": ("IMAGE",),
        },

            "optional": {
                "mask1_opt": ("MASK",),
                "images2_opt": ("IMAGE",),
                "mask2_opt": ("MASK",),
                "images3_opt": ("IMAGE",),
                "mask3_opt": ("MASK",),
                "images4_opt": ("IMAGE",),
                "mask4_opt": ("MASK",),
            },
        }

    RETURN_TYPES = ("IMAGE", "MASK",)

    OUTPUT_NODE = True

    FUNCTION = "doit"

    CATEGORY = "ImpactPack/Util"

    def doit(self, select, images1, mask1_opt=None, images2_opt=None, mask2_opt=None, images3_opt=None, mask3_opt=None,
             images4_opt=None, mask4_opt=None):
        if select == 1:
            return images1, mask1_opt,
        elif select == 2:
            return images2_opt, mask2_opt,
        elif select == 3:
            return images3_opt, mask3_opt,
        else:
            return images4_opt, mask4_opt,

```
