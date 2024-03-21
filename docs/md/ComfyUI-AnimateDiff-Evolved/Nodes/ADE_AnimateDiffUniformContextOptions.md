# Context Options◆Looped Uniform 🎭🅐🅓
## Documentation
- Class name: `ADE_AnimateDiffUniformContextOptions`
- Category: ``
- Output node: `False`

This node is a legacy version of the LoopedUniformContextOptionsNode, designed for creating context options tailored for the AnimateDiff process. It facilitates the generation of context settings that are uniform and looped, optimizing the animation diffusion process by adjusting parameters such as context length, stride, and overlap. This node plays a crucial role in setting up the animation context, ensuring smooth transitions and consistent animation quality.
## Input types
### Required
- **`context_length`**
    - Specifies the length of the context in terms of the number of frames or steps. It is crucial for defining the temporal scope of the animation, affecting the smoothness and continuity of the generated animation.
    - Python dtype: `int`
    - Comfy dtype: `INT`
- **`context_stride`**
    - unknown
    - Python dtype: `unknown`
    - Comfy dtype: `INT`
- **`context_overlap`**
    - unknown
    - Python dtype: `unknown`
    - Comfy dtype: `INT`
- **`context_schedule`**
    - unknown
    - Python dtype: `unknown`
    - Comfy dtype: `['uniform'...]`
- **`closed_loop`**
    - unknown
    - Python dtype: `unknown`
    - Comfy dtype: `BOOLEAN`
### Optional
- **`fuse_method`**
    - unknown
    - Python dtype: `unknown`
    - Comfy dtype: `['pyramid', 'flat'...]`
- **`use_on_equal_length`**
    - unknown
    - Python dtype: `unknown`
    - Comfy dtype: `BOOLEAN`
- **`start_percent`**
    - Determines the starting point of the animation as a percentage of the total length. This parameter allows for fine-tuning the initial phase of the animation, influencing the starting frame or state.
    - Python dtype: `float`
    - Comfy dtype: `FLOAT`
- **`guarantee_steps`**
    - Sets the minimum number of steps to be guaranteed in the animation process. This parameter ensures that the animation reaches a certain length, contributing to the overall stability and consistency of the animation.
    - Python dtype: `int`
    - Comfy dtype: `INT`
- **`prev_context`**
    - Allows for the inclusion of previously defined context options, enabling the chaining or layering of context settings for more complex animation scenarios.
    - Python dtype: `ContextOptionsGroup`
    - Comfy dtype: `CONTEXT_OPTIONS`
- **`view_opts`**
    - unknown
    - Python dtype: `unknown`
    - Comfy dtype: `VIEW_OPTS`
## Output types
- **`context_options`**
    - Returns the updated context options, incorporating the specified settings for the animation diffusion process. This output is essential for configuring the animation context in subsequent steps.
    - Python dtype: `ContextOptionsGroup`
    - Comfy dtype: `CONTEXT_OPTIONS`
## Usage tips
- Infra type: `CPU`
- Common nodes: `ADE_AnimateDiffLoaderWithContext`


## Source code
```python
class LegacyLoopedUniformContextOptionsNode:
    @classmethod
    def INPUT_TYPES(s):
        return {
            "required": {
                "context_length": ("INT", {"default": 16, "min": 1, "max": LENGTH_MAX}),
                "context_stride": ("INT", {"default": 1, "min": 1, "max": STRIDE_MAX}),
                "context_overlap": ("INT", {"default": 4, "min": 0, "max": OVERLAP_MAX}),
                "context_schedule": (ContextSchedules.LEGACY_UNIFORM_SCHEDULE_LIST,),
                "closed_loop": ("BOOLEAN", {"default": False},),
                #"sync_context_to_pe": ("BOOLEAN", {"default": False},),
            },
            "optional": {
                "fuse_method": (ContextFuseMethod.LIST, {"default": ContextFuseMethod.FLAT}),
                "use_on_equal_length": ("BOOLEAN", {"default": False},),
                "start_percent": ("FLOAT", {"default": 0.0, "min": 0.0, "max": 1.0, "step": 0.001}),
                "guarantee_steps": ("INT", {"default": 1, "min": 0, "max": BIGMAX}),
                "prev_context": ("CONTEXT_OPTIONS",),
                "view_opts": ("VIEW_OPTS",),
            }
        }
    
    RETURN_TYPES = ("CONTEXT_OPTIONS",)
    RETURN_NAMES = ("CONTEXT_OPTS",)
    CATEGORY = ""  # No Category, so will not appear in menu
    FUNCTION = "create_options"

    def create_options(self, fuse_method: str=ContextFuseMethod.FLAT, context_schedule: str=None, **kwargs):
        return LoopedUniformContextOptionsNode.create_options(self, fuse_method=fuse_method, **kwargs)

```
