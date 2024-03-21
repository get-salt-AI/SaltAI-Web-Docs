# Context Options◆Looped Uniform 🎭🅐🅓
## Documentation
- Class name: `ADE_LoopedUniformContextOptions`
- Category: `Animate Diff 🎭🅐🅓/context opts`
- Output node: `False`

This node is responsible for creating looped uniform context options for AnimateDiff animations. It allows for the configuration of context parameters such as length, stride, overlap, and whether the loop is closed or open. Additionally, it supports setting a fuse method, adjusting for equal length scenarios, and specifying start percentages and guarantee steps. The node can also inherit and modify previous context settings, making it versatile for creating complex animation sequences.
## Input types
### Required
- **`context_length`**
    - Defines the length of the context in frames. It is crucial for determining the scope of each animation segment within the loop.
    - Python dtype: `int`
    - Comfy dtype: `INT`
- **`context_stride`**
    - Specifies the stride between contexts, affecting how densely the animation frames are sampled.
    - Python dtype: `int`
    - Comfy dtype: `INT`
- **`context_overlap`**
    - Determines the number of frames that overlap between consecutive contexts, influencing the smoothness of transitions.
    - Python dtype: `int`
    - Comfy dtype: `INT`
- **`closed_loop`**
    - Indicates whether the animation loop is closed, ensuring a seamless transition from the end back to the beginning.
    - Python dtype: `bool`
    - Comfy dtype: `BOOLEAN`
### Optional
- **`fuse_method`**
    - The method used to fuse context options, affecting how different contexts are combined.
    - Python dtype: `str`
    - Comfy dtype: `STRING`
- **`use_on_equal_length`**
    - A flag to specify if the context options should be applied even when the animation length matches the context length.
    - Python dtype: `bool`
    - Comfy dtype: `BOOLEAN`
- **`start_percent`**
    - The starting point of the animation as a percentage, allowing for precise control over the animation's initial frame.
    - Python dtype: `float`
    - Comfy dtype: `FLOAT`
- **`guarantee_steps`**
    - Ensures a minimum number of steps in the animation, affecting the animation's duration and complexity.
    - Python dtype: `int`
    - Comfy dtype: `INT`
- **`prev_context`**
    - Previous context settings that can be inherited and modified, enabling the reuse and adaptation of existing configurations.
    - Python dtype: `ContextOptionsGroup`
    - Comfy dtype: `CONTEXT_OPTIONS`
- **`view_opts`**
    - Optional view options that can be applied to the context, providing additional customization for how the animation is viewed.
    - Python dtype: `ContextOptions`
    - Comfy dtype: `VIEW_OPTS`
## Output types
- **`context_options`**
    - The modified or newly created context options, ready for use in generating looped uniform animations.
    - Python dtype: `ContextOptionsGroup`
    - Comfy dtype: `CONTEXT_OPTIONS`
## Usage tips
- Infra type: `CPU`
- Common nodes: unknown


## Source code
```python
class LoopedUniformContextOptionsNode:
    @classmethod
    def INPUT_TYPES(s):
        return {
            "required": {
                "context_length": ("INT", {"default": 16, "min": 1, "max": LENGTH_MAX}),
                "context_stride": ("INT", {"default": 1, "min": 1, "max": STRIDE_MAX}),
                "context_overlap": ("INT", {"default": 4, "min": 0, "max": OVERLAP_MAX}),
                "closed_loop": ("BOOLEAN", {"default": False},),
                #"sync_context_to_pe": ("BOOLEAN", {"default": False},),
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

    def create_options(self, context_length: int, context_stride: int, context_overlap: int, closed_loop: bool,
                       fuse_method: str=ContextFuseMethod.FLAT, use_on_equal_length=False, start_percent: float=0.0, guarantee_steps: int=1,
                       view_opts: ContextOptions=None, prev_context: ContextOptionsGroup=None):
        if prev_context is None:
            prev_context = ContextOptionsGroup()
        prev_context = prev_context.clone()

        context_options = ContextOptions(
            context_length=context_length,
            context_stride=context_stride,
            context_overlap=context_overlap,
            context_schedule=ContextSchedules.UNIFORM_LOOPED,
            closed_loop=closed_loop,
            fuse_method=fuse_method,
            use_on_equal_length=use_on_equal_length,
            start_percent=start_percent,
            guarantee_steps=guarantee_steps,
            view_options=view_opts,
            )
        #context_options.set_sync_context_to_pe(sync_context_to_pe)
        prev_context.add(context_options)
        return (prev_context,)

```
