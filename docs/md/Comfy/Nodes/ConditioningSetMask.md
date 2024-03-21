# Conditioning (Set Mask)
## Documentation
- Class name: `ConditioningSetMask`
- Category: `conditioning`
- Output node: `False`

The `ConditioningSetMask` node is designed to apply a mask to a given conditioning set, potentially altering the area of effect based on the mask's bounds and a specified strength. This operation is useful for selectively applying conditioning to specific regions of an input.
## Input types
### Required
- **`conditioning`**
    - The conditioning set to which the mask will be applied. This parameter is crucial for defining the context or conditions under which certain operations should be performed.
    - Python dtype: `List[Tuple[torch.Tensor, Dict[str, Any]]]`
    - Comfy dtype: `CONDITIONING`
- **`mask`**
    - The mask to be applied to the conditioning set. It defines the area over which the conditioning should be altered.
    - Python dtype: `torch.Tensor`
    - Comfy dtype: `MASK`
- **`strength`**
    - Determines the intensity of the mask's effect on the conditioning set. A higher value means a stronger effect.
    - Python dtype: `float`
    - Comfy dtype: `FLOAT`
- **`set_cond_area`**
    - Specifies whether to set the conditioning area to the mask bounds or use the default setting.
    - Python dtype: `str`
    - Comfy dtype: `STRING`
## Output types
- **`conditioning`**
    - The modified conditioning set with the mask applied.
    - Python dtype: `List[Tuple[torch.Tensor, Dict[str, Any]]]`
    - Comfy dtype: `CONDITIONING`
## Usage tips
- Infra type: `CPU`
- Common nodes: `Attention couple,ConditioningCombine`


## Source code
```python
class ConditioningSetMask:
    @classmethod
    def INPUT_TYPES(s):
        return {"required": {"conditioning": ("CONDITIONING", ),
                              "mask": ("MASK", ),
                              "strength": ("FLOAT", {"default": 1.0, "min": 0.0, "max": 10.0, "step": 0.01}),
                              "set_cond_area": (["default", "mask bounds"],),
                             }}
    RETURN_TYPES = ("CONDITIONING",)
    FUNCTION = "append"

    CATEGORY = "conditioning"

    def append(self, conditioning, mask, set_cond_area, strength):
        c = []
        set_area_to_bounds = False
        if set_cond_area != "default":
            set_area_to_bounds = True
        if len(mask.shape) < 3:
            mask = mask.unsqueeze(0)
        for t in conditioning:
            n = [t[0], t[1].copy()]
            _, h, w = mask.shape
            n[1]['mask'] = mask
            n[1]['set_area_to_bounds'] = set_area_to_bounds
            n[1]['mask_strength'] = strength
            c.append(n)
        return (c, )

```
