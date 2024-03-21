# Context Options◆Standard Static 🎭🅐🅓
## Documentation
- Class name: `ADE_StandardStaticContextOptions`
- Category: `Animate Diff 🎭🅐🅓/context opts`
- Output node: `False`

This node is responsible for creating standard static context options for the Animate Diff process. It configures context options with a fixed view length and overlap, allowing for consistent context scheduling across different iterations or frames.
## Input types
### Required
- **`context_length`**
    - Specifies the total length of the context to be generated, influencing the overall duration of the animation.
    - Python dtype: `int`
    - Comfy dtype: `INT`
- **`context_overlap`**
    - Indicates the overlap between consecutive context segments, aiding in smoother transitions.
    - Python dtype: `int`
    - Comfy dtype: `INT`
### Optional
- **`fuse_method`**
    - Determines the method used to fuse or combine the context segments. This affects how the segments are integrated and can influence the smoothness and continuity of the animation.
    - Python dtype: `str`
    - Comfy dtype: `STRING`
- **`use_on_equal_length`**
    - A flag indicating whether to use the same context length for all segments, ensuring uniformity.
    - Python dtype: `bool`
    - Comfy dtype: `BOOLEAN`
- **`start_percent`**
    - Defines the starting point of the context as a percentage of the total length, allowing for flexible initiation.
    - Python dtype: `float`
    - Comfy dtype: `FLOAT`
- **`guarantee_steps`**
    - Specifies the minimum number of steps to be guaranteed in each context segment, ensuring a base level of detail.
    - Python dtype: `int`
    - Comfy dtype: `INT`
- **`prev_context`**
    - Allows for the inclusion of previously generated context options, enabling continuity across different animation segments.
    - Python dtype: `ContextOptionsGroup`
    - Comfy dtype: `CONTEXT_OPTIONS`
- **`view_opts`**
    - Provides the option to include view-specific settings, further customizing the context generation process.
    - Python dtype: `ContextOptions`
    - Comfy dtype: `VIEW_OPTS`
## Output types
- **`context_options`**
    - The configured context options that specify how the context is structured, including length, overlap, and fusion method for the Animate Diff process.
    - Python dtype: `ContextOptions`
    - Comfy dtype: `CONTEXT_OPTIONS`
- **`ui`**
    - A user interface component that may be generated as part of the output, facilitating interaction with the context options.
## Usage tips
- Infra type: `CPU`
- Common nodes: `ADE_AnimateDiffLoaderGen1,ADE_UseEvolvedSampling`


## Source code
```python
class StandardStaticContextOptionsNode:
    @classmethod
    def INPUT_TYPES(s):
        return {
            "required": {
                "context_length": ("INT", {"default": 16, "min": 1, "max": LENGTH_MAX}),
                "context_overlap": ("INT", {"default": 4, "min": 0, "max": OVERLAP_MAX}),
            },
            "optional": {
                "fuse_method": (ContextFuseMethod.LIST,),
                "use_on_equal_length": ("BOOLEAN", {"default": False},),
                "start_percent": ("FLOAT", {"default": 0.0, "min": 0.0, "max": 1.0, "step": 0.001}),
                "guarantee_steps": ("INT", {"default": 1, "min": 0, "max": BIGMAX}),
                "prev_context": ("CONTEXT_OPTIONS",),
                "view_opts": ("VIEW_OPTS",),
            }
        }
    
    RETURN_TYPES = ("CONTEXT_OPTIONS",)
    RETURN_NAMES = ("CONTEXT_OPTS",)
    CATEGORY = "Animate Diff 🎭🅐🅓/context opts"
    FUNCTION = "create_options"

    def create_options(self, context_length: int, context_overlap: int,
                       fuse_method: str=ContextFuseMethod.PYRAMID, use_on_equal_length=False, start_percent: float=0.0, guarantee_steps: int=1,
                       view_opts: ContextOptions=None, prev_context: ContextOptionsGroup=None):
        if prev_context is None:
            prev_context = ContextOptionsGroup()
        prev_context = prev_context.clone()
        
        context_options = ContextOptions(
            context_length=context_length,
            context_stride=None,
            context_overlap=context_overlap,
            context_schedule=ContextSchedules.STATIC_STANDARD,
            fuse_method=fuse_method,
            use_on_equal_length=use_on_equal_length,
            start_percent=start_percent,
            guarantee_steps=guarantee_steps,
            view_options=view_opts,
            )
        prev_context.add(context_options)
        return (prev_context,)

```
