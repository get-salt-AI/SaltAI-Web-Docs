# View Options◆Looped Uniform 🎭🅐🅓
## Documentation
- Class name: `ADE_LoopedUniformViewOptions`
- Category: `Animate Diff 🎭🅐🅓/context opts/view opts`
- Output node: `False`

This node is designed to create view options for looped uniform context in the Animate Diff process. It configures the parameters such as view length, stride, and overlap, along with the option for a closed loop and the method for fusing context. This setup is crucial for generating animations with a smooth transition between frames, ensuring continuity and uniformity across the animation sequence.
## Input types
### Required
- **`view_length`**
    - Specifies the length of the view, which is critical for determining the number of frames in the animation sequence. It directly influences the duration and smoothness of the generated animation.
    - Python dtype: `int`
    - Comfy dtype: `INT`
- **`view_stride`**
    - Defines the stride between views, affecting how frames are sampled and spaced throughout the animation. It plays a key role in controlling the animation's pace and fluidity.
    - Python dtype: `int`
    - Comfy dtype: `INT`
- **`view_overlap`**
    - Determines the overlap between consecutive views, which is essential for creating seamless transitions between frames in the animation. This parameter helps in achieving a smoother and more continuous animation flow.
    - Python dtype: `int`
    - Comfy dtype: `INT`
- **`closed_loop`**
    - Indicates whether the animation should loop back to the beginning after reaching the end, creating a continuous loop. This option is vital for animations intended to be viewed in a repeating cycle.
    - Python dtype: `bool`
    - Comfy dtype: `BOOLEAN`
### Optional
- **`fuse_method`**
    - Specifies the method used for fusing context, which impacts how different frames are blended together in the animation. This choice affects the visual coherence and consistency of the animation.
    - Python dtype: `str`
    - Comfy dtype: `STRING`
- **`use_on_equal_length`**
    - A flag to determine if the view options should be applied when the animation's start and end frames are of equal length, ensuring flexibility in animation creation.
    - Python dtype: `bool`
    - Comfy dtype: `BOOLEAN`
## Output types
- **`view_opts`**
    - The configured view options for looped uniform context, ready to be used in the Animate Diff process to generate animations.
    - Python dtype: `ContextOptions`
    - Comfy dtype: `VIEW_OPTS`
## Usage tips
- Infra type: `CPU`
- Common nodes: unknown


## Source code
```python
class LoopedUniformViewOptionsNode:
    @classmethod
    def INPUT_TYPES(s):
        return {
            "required": {
                "view_length": ("INT", {"default": 16, "min": 1, "max": LENGTH_MAX}),
                "view_stride": ("INT", {"default": 1, "min": 1, "max": STRIDE_MAX}),
                "view_overlap": ("INT", {"default": 4, "min": 0, "max": OVERLAP_MAX}),
                "closed_loop": ("BOOLEAN", {"default": False},),
            },
            "optional": {
                "fuse_method": (ContextFuseMethod.LIST,),
                "use_on_equal_length": ("BOOLEAN", {"default": False},),
            }
        }
    
    RETURN_TYPES = ("VIEW_OPTS",)
    CATEGORY = "Animate Diff 🎭🅐🅓/context opts/view opts"
    FUNCTION = "create_options"

    def create_options(self, view_length: int, view_overlap: int, view_stride: int, closed_loop: bool,
                       fuse_method: str=ContextFuseMethod.PYRAMID, use_on_equal_length=False):
        view_options = ContextOptions(
            context_length=view_length,
            context_stride=view_stride,
            context_overlap=view_overlap,
            context_schedule=ContextSchedules.UNIFORM_LOOPED,
            closed_loop=closed_loop,
            fuse_method=fuse_method,
            use_on_equal_length=use_on_equal_length,
            )
        return (view_options,)

```
