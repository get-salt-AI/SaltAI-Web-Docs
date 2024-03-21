# Context Options◆Views Only [VRAM⇈] 🎭🅐🅓
## Documentation
- Class name: `ADE_ViewsOnlyContextOptions`
- Category: `Animate Diff 🎭🅐🅓/context opts`
- Output node: `False`

This node is designed to create context options specifically for viewing purposes. It takes a set of view options as input and, based on additional parameters such as start percent and guarantee steps, generates a new context options group that includes the provided view options with the schedule set to view as context. This allows for the customization of how content is viewed, potentially enhancing user experience by adjusting the start point and ensuring a minimum number of steps.
## Input types
### Required
- **`view_opts_req`**
    - The primary input specifying the view options required for creating the context options. This parameter is crucial as it defines the base upon which the new context options are built, influencing how content will be viewed.
    - Python dtype: `ContextOptions`
    - Comfy dtype: `VIEW_OPTS`
### Optional
- **`start_percent`**
    - Determines the starting point as a percentage of the total content, allowing for flexibility in where the viewing begins. This parameter can significantly affect the user's viewing experience by customizing the initial view.
    - Python dtype: `float`
    - Comfy dtype: `FLOAT`
- **`guarantee_steps`**
    - Specifies the minimum number of steps to be guaranteed in the viewing process, ensuring a certain depth or duration of content exploration.
    - Python dtype: `int`
    - Comfy dtype: `INT`
- **`prev_context`**
    - An optional parameter that allows for the inclusion of previous context options, enabling the accumulation and extension of viewing configurations over multiple iterations.
    - Python dtype: `ContextOptionsGroup`
    - Comfy dtype: `CONTEXT_OPTIONS`
## Output types
- **`context_options`**
    - The output is a group of context options that includes the newly created view-based context options. This group can be used to configure and customize the viewing experience further.
    - Python dtype: `ContextOptionsGroup`
    - Comfy dtype: `CONTEXT_OPTIONS`
## Usage tips
- Infra type: `CPU`
- Common nodes: unknown


## Source code
```python
class ViewAsContextOptionsNode:
    @classmethod
    def INPUT_TYPES(s):
        return {
            "required": {
                "view_opts_req": ("VIEW_OPTS",),
            },
            "optional": {
                "start_percent": ("FLOAT", {"default": 0.0, "min": 0.0, "max": 1.0, "step": 0.001}),
                "guarantee_steps": ("INT", {"default": 1, "min": 0, "max": BIGMAX}),
                "prev_context": ("CONTEXT_OPTIONS",),
            }
        }
    
    RETURN_TYPES = ("CONTEXT_OPTIONS",)
    RETURN_NAMES = ("CONTEXT_OPTS",)
    CATEGORY = "Animate Diff 🎭🅐🅓/context opts"
    FUNCTION = "create_options"

    def create_options(self, view_opts_req: ContextOptions, start_percent: float=0.0, guarantee_steps: int=1,
                       prev_context: ContextOptionsGroup=None):
        if prev_context is None:
            prev_context = ContextOptionsGroup()
        prev_context = prev_context.clone()
        context_options = ContextOptions(
            context_schedule=ContextSchedules.VIEW_AS_CONTEXT,
            start_percent=start_percent,
            guarantee_steps=guarantee_steps,
            view_options=view_opts_req,
            use_on_equal_length=True
        )
        prev_context.add(context_options)
        return (prev_context,)

```
