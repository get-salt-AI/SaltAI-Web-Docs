# Context Options◆Standard Uniform 🎭🅐🅓
## Documentation
- Class name: `ADE_StandardUniformContextOptions`
- Category: `Animate Diff 🎭🅐🅓/context opts`
- Output node: `False`

This node is designed to create a set of context options for animation difference (Animate Diff) processes, allowing for uniform distribution of context across iterations. It can incorporate previous context configurations and adjust the starting percentage and guaranteed steps for more precise control over the animation process.
## Input types
### Required
- **`context_length`**
    - Defines the total length of the context to be used in the animation process, setting the scope of the animation.
    - Python dtype: `int`
    - Comfy dtype: `INT`
- **`context_stride`**
    - Specifies the stride length between each context in the animation, affecting the smoothness and speed of the animation.
    - Python dtype: `int`
    - Comfy dtype: `INT`
- **`context_overlap`**
    - Determines the overlap between consecutive contexts, influencing the continuity and fluidity of the animation.
    - Python dtype: `int`
    - Comfy dtype: `INT`
### Optional
- **`fuse_method`**
    - Selects the method for fusing multiple contexts, impacting the final appearance of the animation.
    - Python dtype: `str`
    - Comfy dtype: `STRING`
- **`use_on_equal_length`**
    - Indicates whether to use the context options when the length of the animation is equal to the context length, affecting the applicability of the context settings.
    - Python dtype: `bool`
    - Comfy dtype: `BOOLEAN`
- **`start_percent`**
    - Determines the starting point of the context as a percentage, allowing for fine-tuning of where the animation process begins.
    - Python dtype: `float`
    - Comfy dtype: `FLOAT`
- **`guarantee_steps`**
    - Specifies the minimum number of steps guaranteed to be included in the context, ensuring a certain level of detail and smoothness in the animation.
    - Python dtype: `int`
    - Comfy dtype: `INT`
- **`prev_context`**
    - Allows for the inclusion of previously defined context options, enabling the accumulation and refinement of context settings over multiple iterations.
    - Python dtype: `ContextOptionsGroup`
    - Comfy dtype: `CONTEXT_OPTIONS`
- **`view_opts`**
    - Specifies the view options to be incorporated into the context options, defining additional parameters for the animation process.
    - Python dtype: `ContextOptions`
    - Comfy dtype: `VIEW_OPTS`
## Output types
- **`context_options`**
    - The generated context options, ready to be utilized in the animation difference process.
    - Python dtype: `ContextOptionsGroup`
    - Comfy dtype: `CONTEXT_OPTIONS`
## Usage tips
- Infra type: `CPU`
- Common nodes: `ADE_UseEvolvedSampling`


## Source code
```python
class StandardUniformContextOptionsNode:
    @classmethod
    def INPUT_TYPES(s):
        return {
            "required": {
                "context_length": ("INT", {"default": 16, "min": 1, "max": LENGTH_MAX}),
                "context_stride": ("INT", {"default": 1, "min": 1, "max": STRIDE_MAX}),
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

    def create_options(self, context_length: int, context_stride: int, context_overlap: int,
                       fuse_method: str=ContextFuseMethod.PYRAMID, use_on_equal_length=False, start_percent: float=0.0, guarantee_steps: int=1,
                       view_opts: ContextOptions=None, prev_context: ContextOptionsGroup=None):
        if prev_context is None:
            prev_context = ContextOptionsGroup()
        prev_context = prev_context.clone()

        context_options = ContextOptions(
            context_length=context_length,
            context_stride=context_stride,
            context_overlap=context_overlap,
            context_schedule=ContextSchedules.UNIFORM_STANDARD,
            closed_loop=False,
            fuse_method=fuse_method,
            use_on_equal_length=use_on_equal_length,
            start_percent=start_percent,
            guarantee_steps=guarantee_steps,
            view_options=view_opts,
            )
        prev_context.add(context_options)
        return (prev_context,)

```
