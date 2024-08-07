---
tags:
- Mask
- MaskGeneration
---

# Create Fade Mask
## Documentation
- Class name: `CreateFadeMask`
- Category: `KJNodes/deprecated`
- Output node: `False`

This node is designed to generate fade masks for images, allowing for the creation of transitions between different visual states. It provides the functionality to customize the fade effect through various parameters, enabling precise control over the appearance of the fade.
## Input types
### Required
- **`invert`**
    - A boolean flag that, when set, inverts the fade effect, creating a reverse transition.
    - Comfy dtype: `BOOLEAN`
    - Python dtype: `bool`
- **`frames`**
    - Specifies the number of frames to generate for the fade mask, affecting the length of the transition.
    - Comfy dtype: `INT`
    - Python dtype: `int`
- **`width`**
    - Determines the width of the fade mask, impacting the horizontal dimension of the generated images.
    - Comfy dtype: `INT`
    - Python dtype: `int`
- **`height`**
    - Sets the height of the fade mask, influencing the vertical dimension of the generated images.
    - Comfy dtype: `INT`
    - Python dtype: `int`
- **`interpolation`**
    - Defines the method of interpolation used for the fade effect, allowing for smooth transitions.
    - Comfy dtype: `COMBO[STRING]`
    - Python dtype: `str`
- **`start_level`**
    - The initial opacity level of the fade, setting the starting point of the transition.
    - Comfy dtype: `FLOAT`
    - Python dtype: `float`
- **`midpoint_level`**
    - The opacity level at the midpoint of the transition, allowing for control over the fade's progression.
    - Comfy dtype: `FLOAT`
    - Python dtype: `float`
- **`end_level`**
    - The final opacity level of the fade, determining the end point of the transition.
    - Comfy dtype: `FLOAT`
    - Python dtype: `float`
- **`midpoint_frame`**
    - Specifies the frame at which the midpoint opacity level is reached, controlling the timing of the transition.
    - Comfy dtype: `INT`
    - Python dtype: `int`
## Output types
- **`mask`**
    - Comfy dtype: `MASK`
    - The generated fade mask, which can be used to create transitions between different visual states in images.
    - Python dtype: `torch.Tensor`
## Usage tips
- Infra type: `CPU`
- Common nodes: unknown


## Source code
```python
class CreateFadeMask:
    
    RETURN_TYPES = ("MASK",)
    FUNCTION = "createfademask"
    CATEGORY = "KJNodes/deprecated"

    @classmethod
    def INPUT_TYPES(s):
        return {
            "required": {
                 "invert": ("BOOLEAN", {"default": False}),
                 "frames": ("INT", {"default": 2,"min": 2, "max": 255, "step": 1}),
                 "width": ("INT", {"default": 256,"min": 16, "max": 4096, "step": 1}),
                 "height": ("INT", {"default": 256,"min": 16, "max": 4096, "step": 1}),
                 "interpolation": (["linear", "ease_in", "ease_out", "ease_in_out"],),
                 "start_level": ("FLOAT", {"default": 1.0,"min": 0.0, "max": 1.0, "step": 0.01}),
                 "midpoint_level": ("FLOAT", {"default": 0.5,"min": 0.0, "max": 1.0, "step": 0.01}),
                 "end_level": ("FLOAT", {"default": 0.0,"min": 0.0, "max": 1.0, "step": 0.01}),
                 "midpoint_frame": ("INT", {"default": 0,"min": 0, "max": 4096, "step": 1}),
        },
    } 
    
    def createfademask(self, frames, width, height, invert, interpolation, start_level, midpoint_level, end_level, midpoint_frame):
        def ease_in(t):
            return t * t

        def ease_out(t):
            return 1 - (1 - t) * (1 - t)

        def ease_in_out(t):
            return 3 * t * t - 2 * t * t * t

        batch_size = frames
        out = []
        image_batch = np.zeros((batch_size, height, width), dtype=np.float32)

        if midpoint_frame == 0:
            midpoint_frame = batch_size // 2

        for i in range(batch_size):
            if i <= midpoint_frame:
                t = i / midpoint_frame
                if interpolation == "ease_in":
                    t = ease_in(t)
                elif interpolation == "ease_out":
                    t = ease_out(t)
                elif interpolation == "ease_in_out":
                    t = ease_in_out(t)
                color = start_level - t * (start_level - midpoint_level)
            else:
                t = (i - midpoint_frame) / (batch_size - midpoint_frame)
                if interpolation == "ease_in":
                    t = ease_in(t)
                elif interpolation == "ease_out":
                    t = ease_out(t)
                elif interpolation == "ease_in_out":
                    t = ease_in_out(t)
                color = midpoint_level - t * (midpoint_level - end_level)

            color = np.clip(color, 0, 255)
            image = np.full((height, width), color, dtype=np.float32)
            image_batch[i] = image

        output = torch.from_numpy(image_batch)
        mask = output
        out.append(mask)

        if invert:
            return (1.0 - torch.cat(out, dim=0),)
        return (torch.cat(out, dim=0),)

```
