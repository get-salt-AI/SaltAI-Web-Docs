# AnimateDiff Keyframe 🎭🅐🅓
## Documentation
- Class name: `ADE_AnimateDiffKeyframe`
- Category: `Animate Diff 🎭🅐🅓`
- Output node: `False`

The `ADE_AnimateDiffKeyframe` node is responsible for creating or updating keyframes within the AnimateDiff animation process. It allows for the specification of animation parameters at specific points in the animation timeline, enabling dynamic changes in scale, effect, and other properties as the animation progresses.
## Input types
### Required
- **`start_percent`**
    - Specifies the point in the animation timeline (as a percentage) where the keyframe should be applied. It is crucial for defining the timing of changes in the animation.
    - Python dtype: `float`
    - Comfy dtype: `FLOAT`
### Optional
- **`prev_ad_keyframes`**
    - An optional parameter that allows for the inclusion of previously defined keyframes, enabling the accumulation and modification of animation settings over time.
    - Python dtype: `ADKeyframeGroup`
    - Comfy dtype: `AD_KEYFRAMES`
- **`scale_multival`**
    - Defines a scaling factor or a tensor of scaling factors to be applied at the specified keyframe, affecting the size or intensity of the animated effect.
    - Python dtype: `Union[float, torch.Tensor]`
    - Comfy dtype: `MULTIVAL`
- **`effect_multival`**
    - Specifies an effect factor or a tensor of effect factors to be applied at the keyframe, influencing the visual or behavioral aspects of the animation.
    - Python dtype: `Union[float, torch.Tensor]`
    - Comfy dtype: `MULTIVAL`
- **`inherit_missing`**
    - Determines whether missing values for scale and effect should be inherited from the previous keyframe, ensuring continuity in the animation.
    - Python dtype: `bool`
    - Comfy dtype: `BOOLEAN`
- **`guarantee_steps`**
    - Guarantees a minimum number of steps for which the specified keyframe settings will be applied, ensuring that the animation effect is noticeable.
    - Python dtype: `int`
    - Comfy dtype: `INT`
## Output types
- **`ad_keyframes`**
    - Returns the updated collection of keyframes, including the newly added or modified keyframe, facilitating the construction of complex animations.
    - Python dtype: `ADKeyframeGroup`
    - Comfy dtype: `AD_KEYFRAMES`
## Usage tips
- Infra type: `CPU`
- Common nodes: unknown


## Source code
```python
class ADKeyframeNode:
    @classmethod
    def INPUT_TYPES(s):
        return {
            "required": {
                "start_percent": ("FLOAT", {"default": 0.0, "min": 0.0, "max": 1.0, "step": 0.001}, ),
            },
            "optional": {
                "prev_ad_keyframes": ("AD_KEYFRAMES", ),
                "scale_multival": ("MULTIVAL",),
                "effect_multival": ("MULTIVAL",),
                "inherit_missing": ("BOOLEAN", {"default": True}, ),
                "guarantee_steps": ("INT", {"default": 1, "min": 0, "max": BIGMAX}),
            }
        }
    
    RETURN_TYPES = ("AD_KEYFRAMES", )
    FUNCTION = "load_keyframe"

    CATEGORY = "Animate Diff 🎭🅐🅓"

    def load_keyframe(self,
                      start_percent: float, prev_ad_keyframes=None,
                      scale_multival: [float, torch.Tensor]=None, effect_multival: [float, torch.Tensor]=None,
                      inherit_missing: bool=True, guarantee_steps: int=1):
        if not prev_ad_keyframes:
            prev_ad_keyframes = ADKeyframeGroup()
        prev_ad_keyframes = prev_ad_keyframes.clone()
        keyframe = ADKeyframe(start_percent=start_percent, scale_multival=scale_multival, effect_multival=effect_multival,
                              inherit_missing=inherit_missing, guarantee_steps=guarantee_steps)
        prev_ad_keyframes.add(keyframe)
        return (prev_ad_keyframes,)

```
