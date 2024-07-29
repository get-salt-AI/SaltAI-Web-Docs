---
tags:
- MotionData
---

# SparseCtrl Weight Extras 🛂🅐🅒🅝
## Documentation
- Class name: `ACN_SparseCtrlWeightExtras`
- Category: `Adv-ControlNet 🛂🅐🅒🅝/SparseCtrl/extras`
- Output node: `False`

This node is designed to create and manage additional weight parameters for Sparse Control Networks, enhancing their flexibility and control over the generation process. It allows for the adjustment of weights related to hints, non-hints, and masks within the network, facilitating fine-tuned control over the sparse conditioning aspects.
## Input types
### Optional
- **`cn_extras`**
    - A dictionary of existing extra weights for the ControlNet, allowing for the extension or modification of these weights based on the sparse control requirements.
    - Comfy dtype: `CN_WEIGHTS_EXTRAS`
    - Python dtype: `dict[str]`
- **`sparse_hint_mult`**
    - Multiplier for hint-based weights, influencing how strongly hints affect the generation process.
    - Comfy dtype: `FLOAT`
    - Python dtype: `float`
- **`sparse_nonhint_mult`**
    - Multiplier for non-hint weights, adjusting the influence of non-hint elements in the generation.
    - Comfy dtype: `FLOAT`
    - Python dtype: `float`
- **`sparse_mask_mult`**
    - Multiplier for mask weights, controlling the impact of masks on the generation outcome.
    - Comfy dtype: `FLOAT`
    - Python dtype: `float`
## Output types
- **`cn_extras`**
    - Comfy dtype: `CN_WEIGHTS_EXTRAS`
    - The updated dictionary of extra weights for the ControlNet, including adjustments made to hint, non-hint, and mask multipliers.
    - Python dtype: `dict[str]`
## Usage tips
- Infra type: `CPU`
- Common nodes: unknown


## Source code
```python
class SparseWeightExtras:
    @classmethod
    def INPUT_TYPES(s):
        return {
            "optional": {
                "cn_extras": ("CN_WEIGHTS_EXTRAS",),
                "sparse_hint_mult": ("FLOAT", {"default": 1.0, "min": 0.0, "max": 10.0, "step": 0.001}, ),
                "sparse_nonhint_mult": ("FLOAT", {"default": 1.0, "min": 0.0, "max": 10.0, "step": 0.001}, ),
                "sparse_mask_mult": ("FLOAT", {"default": 1.0, "min": 0.0, "max": 10.0, "step": 0.001}, ),
            }
        }
    
    RETURN_TYPES = ("CN_WEIGHTS_EXTRAS", )
    RETURN_NAMES = ("cn_extras", )
    FUNCTION = "create_weight_extras"

    CATEGORY = "Adv-ControlNet 🛂🅐🅒🅝/SparseCtrl/extras"

    def create_weight_extras(self, cn_extras: dict[str]={}, sparse_hint_mult=1.0, sparse_nonhint_mult=1.0, sparse_mask_mult=1.0):
        cn_extras = cn_extras.copy()
        cn_extras[SparseConst.HINT_MULT] = sparse_hint_mult
        cn_extras[SparseConst.NONHINT_MULT] = sparse_nonhint_mult
        cn_extras[SparseConst.MASK_MULT] = sparse_mask_mult
        return (cn_extras, )

```
