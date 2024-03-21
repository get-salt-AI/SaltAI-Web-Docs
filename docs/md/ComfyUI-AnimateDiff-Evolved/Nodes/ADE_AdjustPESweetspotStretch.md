# Adjust PE [Sweetspot Stretch] 🎭🅐🅓
## Documentation
- Class name: `ADE_AdjustPESweetspotStretch`
- Category: `Animate Diff 🎭🅐🅓/ad settings/pe adjust`
- Output node: `False`

This node is designed to adjust the positional encoding (PE) sweetspot in the context of animating differences. It allows for the modification of the initial sweetspot and setting a new sweetspot value, optionally printing the adjustment process. This adjustment is crucial for fine-tuning the animation effect by altering the base positional encoding parameters.
## Input types
### Required
- **`sweetspot`**
    - Defines the initial sweetspot value for positional encoding, serving as a baseline for adjustments.
    - Python dtype: `int`
    - Comfy dtype: `INT`
- **`new_sweetspot`**
    - Sets the new sweetspot value for positional encoding, allowing for dynamic adjustment of the animation's base encoding.
    - Python dtype: `int`
    - Comfy dtype: `INT`
- **`print_adjustment`**
    - A flag to enable or disable printing of the adjustment process, useful for debugging or monitoring.
    - Python dtype: `bool`
    - Comfy dtype: `BOOLEAN`
### Optional
- **`prev_pe_adjust`**
    - Optional previous positional encoding adjustment to be considered in the current adjustment process.
    - Python dtype: `AdjustPEGroup or None`
    - Comfy dtype: `PE_ADJUST`
## Output types
- **`pe_adjust`**
    - The adjusted positional encoding settings, encapsulating the sweetspot adjustments.
    - Python dtype: `AdjustPEGroup`
    - Comfy dtype: `PE_ADJUST`
## Usage tips
- Infra type: `CPU`
- Common nodes: unknown


## Source code
```python
class SweetspotStretchPENode:
    @classmethod
    def INPUT_TYPES(s):
        return {
            "required": {
                "sweetspot": ("INT", {"default": 16, "min": 0, "max": BIGMAX},),
                "new_sweetspot": ("INT", {"default": 16, "min": 0, "max": BIGMAX},),
                "print_adjustment": ("BOOLEAN", {"default": False}),
            },
            "optional": {
                "prev_pe_adjust": ("PE_ADJUST",),
            }
        }
    
    RETURN_TYPES = ("PE_ADJUST",)
    CATEGORY = "Animate Diff 🎭🅐🅓/ad settings/pe adjust"
    FUNCTION = "get_pe_adjust"

    def get_pe_adjust(self, sweetspot: int, new_sweetspot: int, print_adjustment: bool, prev_pe_adjust: AdjustPEGroup=None):
        if prev_pe_adjust is None:
            prev_pe_adjust = AdjustPEGroup()
        prev_pe_adjust = prev_pe_adjust.clone()
        adjust = AdjustPE(cap_initial_pe_length=sweetspot, interpolate_pe_to_length=new_sweetspot,
                          print_adjustment=print_adjustment)
        prev_pe_adjust.add(adjust)
        return (prev_pe_adjust,)

```
