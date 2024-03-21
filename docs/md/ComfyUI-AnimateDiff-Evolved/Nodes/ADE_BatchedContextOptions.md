# Context Options◆Batched [Non-AD] 🎭🅐🅓
## Documentation
- Class name: `ADE_BatchedContextOptions`
- Category: `Animate Diff 🎭🅐🅓/context opts`
- Output node: `False`

This node is designed to create and manage batched context options for AnimateDiff operations. It allows for the customization of context length, start percent, and guarantee steps, optionally incorporating previous context settings. The node is particularly useful in scenarios where batch processing of context options is required, enhancing efficiency and flexibility in AnimateDiff workflows.
## Input types
### Required
- **`context_length`**
    - Specifies the length of the context to be used in the AnimateDiff operation. It is a crucial parameter as it determines the amount of information to be considered for each step of the animation process.
    - Python dtype: `int`
    - Comfy dtype: `INT`
### Optional
- **`start_percent`**
    - Defines the starting point of the context as a percentage of the total length. This allows for fine-tuning the initial position of the context window, offering control over the animation's starting frame.
    - Python dtype: `float`
    - Comfy dtype: `FLOAT`
- **`guarantee_steps`**
    - Ensures a minimum number of steps to be processed, regardless of the context length. This parameter guarantees that a certain amount of animation frames will be generated, providing consistency in output.
    - Python dtype: `int`
    - Comfy dtype: `INT`
- **`prev_context`**
    - Allows for the inclusion of previously defined context options, enabling the accumulation and modification of context settings over multiple iterations. This facilitates the creation of complex animation sequences.
    - Python dtype: `ContextOptionsGroup`
    - Comfy dtype: `CONTEXT_OPTIONS`
## Output types
- **`context_options`**
    - Returns the updated context options, incorporating the specified parameters and any previous context settings. This output is essential for guiding the AnimateDiff process, ensuring that the animation adheres to the defined context parameters.
    - Python dtype: `ContextOptionsGroup`
    - Comfy dtype: `CONTEXT_OPTIONS`
## Usage tips
- Infra type: `CPU`
- Common nodes: unknown


## Source code
```python
class BatchedContextOptionsNode:
    @classmethod
    def INPUT_TYPES(s):
        return {
            "required": {
                "context_length": ("INT", {"default": 16, "min": 1, "max": LENGTH_MAX}),
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

    def create_options(self, context_length: int, start_percent: float=0.0, guarantee_steps: int=1,
                       prev_context: ContextOptionsGroup=None):
        if prev_context is None:
            prev_context = ContextOptionsGroup()
        prev_context = prev_context.clone()
        
        context_options = ContextOptions(
            context_length=context_length,
            context_overlap=0,
            context_schedule=ContextSchedules.BATCHED,
            start_percent=start_percent,
            guarantee_steps=guarantee_steps,
            )
        prev_context.add(context_options)
        return (prev_context,)

```
