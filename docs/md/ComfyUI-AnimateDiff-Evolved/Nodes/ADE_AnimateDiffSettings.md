# AnimateDiff Settings 🎭🅐🅓
## Documentation
- Class name: `ADE_AnimateDiffSettings`
- Category: `Animate Diff 🎭🅐🅓/ad settings`
- Output node: `False`

This node is responsible for configuring the AnimateDiff settings, which likely includes parameters for adjusting the perceptual encoding (PE) used in the animation process. It serves as a foundational element in customizing how animations are generated, allowing for adjustments to the animation's appearance or behavior.
## Input types
### Optional
- **`pe_adjust`**
    - Specifies the adjustments to be made to the perceptual encoding (PE) settings. This can significantly influence the final animation by altering aspects such as stretch, strength, or attention mechanisms.
    - Python dtype: `AdjustPEGroup`
    - Comfy dtype: `PE_ADJUST`
## Output types
- **`ad_settings`**
    - Returns the configured AnimateDiff settings, encapsulating all adjustments made to the perceptual encoding and potentially other parameters influencing the animation process.
    - Python dtype: `AnimateDiffSettings`
    - Comfy dtype: `AD_SETTINGS`
## Usage tips
- Infra type: `CPU`
- Common nodes: unknown


## Source code
```python
class AnimateDiffSettingsNode:
    @classmethod
    def INPUT_TYPES(s):
        return {
            "optional": {
                "pe_adjust": ("PE_ADJUST",),
            }
        }
    
    RETURN_TYPES = ("AD_SETTINGS",)
    CATEGORY = "Animate Diff 🎭🅐🅓/ad settings"
    FUNCTION = "get_ad_settings"

    def get_ad_settings(self, pe_adjust: AdjustPEGroup=None):
        return (AnimateDiffSettings(adjust_pe=pe_adjust),)

```
