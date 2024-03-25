# Context Options◆Standard Static 🎭🅐🅓
## Documentation
- Class name: `ADE_StandardStaticContextOptions`
- Category: `Animate Diff 🎭🅐🅓/context opts`
- Output node: `False`

This node is designed to generate a set of static context options for the AnimateDiff process, allowing for the customization of animation generation through predefined context settings. It focuses on providing a consistent and unchanging context configuration throughout the animation process.
## Input types
### Required
- **`context_length`**
    - Comfy dtype: `INT`
    - Specifies the length of the context, determining the size of the context window used in the animation process. It is crucial for defining the scope of each animation frame.
    - Python dtype: `int`
- **`context_overlap`**
    - Comfy dtype: `INT`
    - Defines the amount of overlap between consecutive contexts in the animation, affecting the smoothness and continuity of the generated animation.
    - Python dtype: `int`
### Optional
- **`fuse_method`**
    - Comfy dtype: `COMBO[STRING]`
    - Determines the method used to fuse context options, offering flexibility in how animation frames are combined and processed.
    - Python dtype: `str`
- **`use_on_equal_length`**
    - Comfy dtype: `BOOLEAN`
    - Indicates whether to use the view options on sequences of equal length, affecting the applicability of the context settings.
    - Python dtype: `bool`
- **`start_percent`**
    - Comfy dtype: `FLOAT`
    - Specifies the starting percentage of the animation, influencing the initial frame of the animation process.
    - Python dtype: `float`
- **`guarantee_steps`**
    - Comfy dtype: `INT`
    - Defines the minimum number of steps guaranteed in the animation, ensuring a certain length of the generated animation.
    - Python dtype: `int`
- **`prev_context`**
    - Comfy dtype: `CONTEXT_OPTIONS`
    - Allows for the inclusion of previous context settings, enabling the continuation or modification of existing animation configurations.
    - Python dtype: `ContextOptionsGroup`
- **`view_opts`**
    - Comfy dtype: `VIEW_OPTS`
    - Specifies the view options to be used, providing additional customization for the animation generation.
    - Python dtype: `ContextOptions`
## Output types
- **`CONTEXT_OPTS`**
    - Comfy dtype: `CONTEXT_OPTIONS`
    - Outputs the configured context options, encapsulating the static context settings tailored for the animation generation.
    - Python dtype: `ContextOptionsGroup`
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
