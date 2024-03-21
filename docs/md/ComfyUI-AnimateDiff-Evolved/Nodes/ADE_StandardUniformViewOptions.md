# View Options◆Standard Uniform 🎭🅐🅓
## Documentation
- Class name: `ADE_StandardUniformViewOptions`
- Category: `Animate Diff 🎭🅐🅓/context opts/view opts`
- Output node: `False`

This node generates a set of view options for animating differences in a uniform manner. It configures the length, stride, and overlap of views, along with a fusion method to blend these views, creating a standardized context for animation processes.
## Input types
### Required
- **`view_length`**
    - Specifies the length of each view, influencing the granularity of the animation process.
    - Python dtype: `int`
    - Comfy dtype: `INT`
- **`view_stride`**
    - Determines the step size between consecutive views, affecting the smoothness of the animation.
    - Python dtype: `int`
    - Comfy dtype: `INT`
- **`view_overlap`**
    - Defines how much consecutive views overlap, which can affect the continuity and smoothness of the animation.
    - Python dtype: `int`
    - Comfy dtype: `INT`
### Optional
- **`fuse_method`**
    - The method used to blend or fuse consecutive views, impacting the overall visual coherence of the animation.
    - Python dtype: `str`
    - Comfy dtype: `STRING`
## Output types
- **`view_opts`**
    - The generated view options, encapsulating the configured parameters for animation.
    - Python dtype: `ContextOptions`
    - Comfy dtype: `VIEW_OPTS`
## Usage tips
- Infra type: `CPU`
- Common nodes: unknown


## Source code
```python
class StandardUniformViewOptionsNode:
    @classmethod
    def INPUT_TYPES(s):
        return {
            "required": {
                "view_length": ("INT", {"default": 16, "min": 1, "max": LENGTH_MAX}),
                "view_stride": ("INT", {"default": 1, "min": 1, "max": STRIDE_MAX}),
                "view_overlap": ("INT", {"default": 4, "min": 0, "max": OVERLAP_MAX}),
            },
            "optional": {
                "fuse_method": (ContextFuseMethod.LIST,),
            }
        }
    
    RETURN_TYPES = ("VIEW_OPTS",)
    CATEGORY = "Animate Diff 🎭🅐🅓/context opts/view opts"
    FUNCTION = "create_options"

    def create_options(self, view_length: int, view_overlap: int, view_stride: int,
                       fuse_method: str=ContextFuseMethod.PYRAMID,):
        view_options = ContextOptions(
            context_length=view_length,
            context_stride=view_stride,
            context_overlap=view_overlap,
            context_schedule=ContextSchedules.UNIFORM_STANDARD,
            fuse_method=fuse_method,
            )
        return (view_options,)

```
