# Adjust PE [Manual] 🎭🅐🅓
## Documentation
- Class name: `ADE_AdjustPEManual`
- Category: `Animate Diff 🎭🅐🅓/ad settings/pe adjust`
- Output node: `False`

The ADE_AdjustPEManual node allows for manual adjustment of positional encoding (PE) parameters within the AnimateDiff framework. It provides a flexible interface for users to specify various PE adjustments, such as stretching, offsetting, and capping the initial PE length, which can significantly influence the animation generation process by modifying how the model perceives temporal information.
## Input types
### Required
- **`cap_initial_pe_length`**
    - Specifies the maximum length for the initial positional encoding. This parameter can be used to limit the PE length at the beginning of the animation, affecting the model's understanding of temporal dynamics.
    - Python dtype: `int`
    - Comfy dtype: `INT`
- **`interpolate_pe_to_length`**
    - Determines the length to which the positional encoding should be interpolated. This adjustment allows for the stretching or compressing of the PE, influencing the animation's perceived speed and timing.
    - Python dtype: `int`
    - Comfy dtype: `INT`
- **`initial_pe_idx_offset`**
    - Sets the starting index offset for the positional encoding. This can be used to skip initial frames, effectively starting the animation from a later point.
    - Python dtype: `int`
    - Comfy dtype: `INT`
- **`final_pe_idx_offset`**
    - Defines the ending index offset for the positional encoding, allowing for the exclusion of final frames and thus shortening the animation.
    - Python dtype: `int`
    - Comfy dtype: `INT`
- **`print_adjustment`**
    - A flag to enable or disable printing of the adjustment details. Useful for debugging or understanding the impact of the adjustments made.
    - Python dtype: `bool`
    - Comfy dtype: `BOOLEAN`
### Optional
- **`prev_pe_adjust`**
    - Allows for chaining of positional encoding adjustments by taking a previous adjustment group as input. This enables complex and cumulative modifications to the PE.
    - Python dtype: `AdjustPEGroup`
    - Comfy dtype: `PE_ADJUST`
## Output types
- **`pe_adjust`**
    - Returns the updated group of positional encoding adjustments, incorporating the manual adjustments specified by the input parameters.
    - Python dtype: `AdjustPEGroup`
    - Comfy dtype: `PE_ADJUST`
## Usage tips
- Infra type: `CPU`
- Common nodes: unknown


## Source code
```python
class ManualAdjustPENode:
    @classmethod
    def INPUT_TYPES(s):
        return {
            "required": {
                "cap_initial_pe_length": ("INT", {"default": 0, "min": 0, "step": 1}),
                "interpolate_pe_to_length": ("INT", {"default": 0, "min": 0, "step": 1}),
                "initial_pe_idx_offset": ("INT", {"default": 0, "min": 0, "step": 1}),
                "final_pe_idx_offset": ("INT", {"default": 0, "min": 0, "step": 1}),
                "print_adjustment": ("BOOLEAN", {"default": False}),
            },
            "optional": {
                "prev_pe_adjust": ("PE_ADJUST",),
            }
        }
    
    RETURN_TYPES = ("PE_ADJUST",)
    CATEGORY = "Animate Diff 🎭🅐🅓/ad settings/pe adjust"
    FUNCTION = "get_pe_adjust"

    def get_pe_adjust(self, cap_initial_pe_length: int, interpolate_pe_to_length: int, 
                      initial_pe_idx_offset: int, final_pe_idx_offset: int, print_adjustment: bool,
                      prev_pe_adjust: AdjustPEGroup=None):
        if prev_pe_adjust is None:
            prev_pe_adjust = AdjustPEGroup()
        prev_pe_adjust = prev_pe_adjust.clone()
        adjust = AdjustPE(cap_initial_pe_length=cap_initial_pe_length, interpolate_pe_to_length=interpolate_pe_to_length,
                          initial_pe_idx_offset=initial_pe_idx_offset, final_pe_idx_offset=final_pe_idx_offset,
                          print_adjustment=print_adjustment)
        prev_pe_adjust.add(adjust)
        return (prev_pe_adjust,)

```
