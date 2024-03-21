# View Options◆Standard Static 🎭🅐🅓
## Documentation
- Class name: `ADE_StandardStaticViewOptions`
- Category: `Animate Diff 🎭🅐🅓/context opts/view opts`
- Output node: `False`

This node generates static view options for Animate Diff context, specifying the length and overlap of views. It allows for the customization of how animation frames are generated and overlapped, with an optional fuse method to determine how frames are combined.
## Input types
### Required
- **`view_length`**
    - Defines the total length of the view, impacting the number of frames generated for the animation.
    - Python dtype: `int`
    - Comfy dtype: `INT`
- **`view_overlap`**
    - Specifies the amount of overlap between consecutive views, affecting the smoothness of the animation transition.
    - Python dtype: `int`
    - Comfy dtype: `INT`
### Optional
- **`fuse_method`**
    - Determines the method used to combine frames, influencing the final appearance of the animation.
    - Python dtype: `str`
    - Comfy dtype: `STRING`
## Output types
- **`view_opts`**
    - The generated view options, encapsulating the configuration for static view generation in Animate Diff context.
    - Python dtype: `ContextOptions`
    - Comfy dtype: `VIEW_OPTS`
## Usage tips
- Infra type: `CPU`
- Common nodes: unknown


## Source code
```python
class StandardStaticViewOptionsNode:
    @classmethod
    def INPUT_TYPES(s):
        return {
            "required": {
                "view_length": ("INT", {"default": 16, "min": 1, "max": LENGTH_MAX}),
                "view_overlap": ("INT", {"default": 4, "min": 0, "max": OVERLAP_MAX}),
            },
            "optional": {
                "fuse_method": (ContextFuseMethod.LIST,),
            }
        }
    
    RETURN_TYPES = ("VIEW_OPTS",)
    CATEGORY = "Animate Diff 🎭🅐🅓/context opts/view opts"
    FUNCTION = "create_options"

    def create_options(self, view_length: int, view_overlap: int,
                       fuse_method: str=ContextFuseMethod.FLAT,):
        view_options = ContextOptions(
            context_length=view_length,
            context_stride=None,
            context_overlap=view_overlap,
            context_schedule=ContextSchedules.STATIC_STANDARD,
            fuse_method=fuse_method,
            )
        return (view_options,)

```
