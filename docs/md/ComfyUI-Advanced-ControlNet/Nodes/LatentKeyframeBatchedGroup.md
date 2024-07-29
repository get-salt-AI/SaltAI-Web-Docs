---
tags:
- Counting
- Curve
- Interpolation
- WavePatterns
---

# Latent Keyframe From List 🛂🅐🅒🅝
## Documentation
- Class name: `LatentKeyframeBatchedGroup`
- Category: `Adv-ControlNet 🛂🅐🅒🅝/keyframes`
- Output node: `False`

The LatentKeyframeBatchedGroup node is designed for managing and manipulating groups of latent keyframes within a batch processing context. It facilitates the addition, replacement, and organization of keyframes based on their batch indices, supporting operations like cloning existing groups for modification or creating new groups from iterable strength values. This node plays a crucial role in the dynamic adjustment of keyframe strengths across batches, enabling complex animation and transformation effects in generative models.
## Input types
### Required
- **`float_strengths`**
    - Represents the strengths of keyframes to be added or modified in the batch. It can be a single float value or an iterable of float values, dictating the strength of each keyframe in the batch, thereby influencing the animation or transformation effect.
    - Comfy dtype: `FLOAT`
    - Python dtype: `Union[float, Iterable[float]]`
### Optional
- **`prev_latent_kf`**
    - An optional parameter that allows for the continuation or modification of an existing group of latent keyframes. If provided, the current operation will build upon or modify this existing group, enabling sequential or layered adjustments to keyframe strengths.
    - Comfy dtype: `LATENT_KEYFRAME`
    - Python dtype: `LatentKeyframeGroup`
- **`print_keyframes`**
    - A boolean flag that, when set to True, triggers logging of the keyframe details. This is useful for debugging or for detailed insight into the keyframe adjustments being made.
    - Comfy dtype: `BOOLEAN`
    - Python dtype: `bool`
## Output types
- **`LATENT_KF`**
    - Comfy dtype: `LATENT_KEYFRAME`
    - The resulting group of latent keyframes after processing. This includes any new keyframes added or modifications made to existing keyframes, organized and ready for further use in animation or transformation processes.
    - Python dtype: `LatentKeyframeGroup`
## Usage tips
- Infra type: `CPU`
- Common nodes: unknown


## Source code
```python
class LatentKeyframeBatchedGroupNode:
    @classmethod
    def INPUT_TYPES(s):
        return {
            "required": {
                "float_strengths": ("FLOAT", {"default": -1, "min": -1, "step": 0.001, "forceInput": True}),
            },
            "optional": {
                "prev_latent_kf": ("LATENT_KEYFRAME", ),
                "print_keyframes": ("BOOLEAN", {"default": False})
            }
        }

    RETURN_NAMES = ("LATENT_KF", )
    RETURN_TYPES = ("LATENT_KEYFRAME", )
    FUNCTION = "load_keyframe"
    CATEGORY = "Adv-ControlNet 🛂🅐🅒🅝/keyframes"

    def load_keyframe(self, float_strengths: Union[float, list[float]],
                      prev_latent_kf: LatentKeyframeGroup=None,
                      prev_latent_keyframe: LatentKeyframeGroup=None, # old name
                      print_keyframes=False):
        prev_latent_keyframe = prev_latent_keyframe if prev_latent_keyframe else prev_latent_kf
        if not prev_latent_keyframe:
            prev_latent_keyframe = LatentKeyframeGroup()
        else:
            prev_latent_keyframe = prev_latent_keyframe.clone()
        curr_latent_keyframe = LatentKeyframeGroup()

        # if received a normal float input, do nothing
        if type(float_strengths) in (float, int):
            logger.info("No batched float_strengths passed into Latent Keyframe Batch Group node; will not create any new keyframes.")
        # if iterable, attempt to create LatentKeyframes with chosen strengths
        elif isinstance(float_strengths, Iterable):
            for idx, strength in enumerate(float_strengths):
                keyframe = LatentKeyframe(idx, strength)
                curr_latent_keyframe.add(keyframe)
        else:
            raise ValueError(f"Expected strengths to be an iterable input, but was {type(float_strengths).__repr__}.")    

        if print_keyframes:
            for keyframe in curr_latent_keyframe.keyframes:
                logger.info(f"LatentKeyframe {keyframe.batch_index}={keyframe.strength}")

        # replace values with prev_latent_keyframes
        for latent_keyframe in prev_latent_keyframe.keyframes:
            curr_latent_keyframe.add(latent_keyframe)

        return (curr_latent_keyframe,)

```
