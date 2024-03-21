# Adjust PE [Full Stretch] 🎭🅐🅓
## Documentation
- Class name: `ADE_AdjustPEFullStretch`
- Category: `Animate Diff 🎭🅐🅓/ad settings/pe adjust`
- Output node: `False`

This node is designed to adjust the positional encoding (PE) of a model by stretching it. It allows for the modification of the model's PE based on specified parameters, such as the amount of stretch to apply. This adjustment can be crucial for tasks that require altering the model's understanding of position or sequence length, such as in animation or sequence generation tasks.
## Input types
### Required
- **`pe_stretch`**
    - Specifies the amount by which the positional encoding should be stretched. This parameter directly influences the model's perception of sequence length and can be pivotal for adjusting the animation or sequence generation.
    - Python dtype: `int`
    - Comfy dtype: `INT`
- **`print_adjustment`**
    - A boolean flag that, when set to True, enables the logging of adjustments made to the positional encoding. This can be useful for debugging or understanding the modifications applied.
    - Python dtype: `bool`
    - Comfy dtype: `BOOLEAN`
### Optional
- **`prev_pe_adjust`**
    - An optional parameter that allows for the accumulation of positional encoding adjustments. If provided, the current adjustments will be added to this existing group of adjustments.
    - Python dtype: `AdjustPEGroup or None`
    - Comfy dtype: `PE_ADJUST`
## Output types
- **`pe_adjust`**
    - Returns an updated group of positional encoding adjustments, incorporating the adjustments specified in the input parameters.
    - Python dtype: `AdjustPEGroup`
    - Comfy dtype: `PE_ADJUST`
## Usage tips
- Infra type: `CPU`
- Common nodes: unknown


## Source code
```python
class FullStretchPENode:
    @classmethod
    def INPUT_TYPES(s):
        return {
            "required": {
                "pe_stretch": ("INT", {"default": 0, "min": 0, "max": BIGMAX},),
                "print_adjustment": ("BOOLEAN", {"default": False}),
            },
            "optional": {
                "prev_pe_adjust": ("PE_ADJUST",),
            }
        }
    
    RETURN_TYPES = ("PE_ADJUST",)
    CATEGORY = "Animate Diff 🎭🅐🅓/ad settings/pe adjust"
    FUNCTION = "get_pe_adjust"

    def get_pe_adjust(self, pe_stretch: int, print_adjustment: bool, prev_pe_adjust: AdjustPEGroup=None):
        if prev_pe_adjust is None:
            prev_pe_adjust = AdjustPEGroup()
        prev_pe_adjust = prev_pe_adjust.clone()
        adjust = AdjustPE(motion_pe_stretch=pe_stretch,
                          print_adjustment=print_adjustment)
        prev_pe_adjust.add(adjust)
        return (prev_pe_adjust,)

```
