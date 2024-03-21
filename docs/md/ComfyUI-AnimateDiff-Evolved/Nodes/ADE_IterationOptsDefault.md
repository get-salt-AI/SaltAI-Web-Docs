# Default Iteration Options 🎭🅐🅓
## Documentation
- Class name: `ADE_IterationOptsDefault`
- Category: `Animate Diff 🎭🅐🅓/iteration opts`
- Output node: `False`

This node is responsible for creating default iteration options for the AnimateDiff process. It configures the iteration parameters such as the number of iterations, filter settings, and various other parameters that influence the behavior of the animation generation process.
## Input types
### Required
- **`iterations`**
    - Specifies the number of iterations to perform. This parameter directly influences the length and detail of the animation process.
    - Python dtype: `int`
    - Comfy dtype: `INT`
### Optional
- **`iter_batch_offset`**
    - Offsets the batch number for each iteration, allowing for customization of batch processing during animation.
    - Python dtype: `int`
    - Comfy dtype: `INT`
- **`iter_seed_offset`**
    - Offsets the seed value for each iteration, enabling controlled variation in the animation generation.
    - Python dtype: `int`
    - Comfy dtype: `INT`
## Output types
- **`iteration_opts`**
    - The configured iteration options, encapsulating all the specified settings for the animation process.
    - Python dtype: `IterationOptions`
    - Comfy dtype: `ITERATION_OPTS`
## Usage tips
- Infra type: `CPU`
- Common nodes: unknown


## Source code
```python
class IterationOptionsNode:
    @classmethod
    def INPUT_TYPES(s):
        return {
            "required": {
                "iterations": ("INT", {"default": 1, "min": 1}),
            },
            "optional": {
                "iter_batch_offset": ("INT", {"default": 0, "min": 0, "max": BIGMAX}),
                "iter_seed_offset": ("INT", {"default": 0, "min": BIGMIN, "max": BIGMAX}),
            }
        }
    
    RETURN_TYPES = ("ITERATION_OPTS",)
    CATEGORY = "Animate Diff 🎭🅐🅓/iteration opts"
    FUNCTION = "create_iter_opts"

    def create_iter_opts(self, iterations: int, iter_batch_offset: int=0, iter_seed_offset: int=0):
        iter_opts = IterationOptions(iterations=iterations, iter_batch_offset=iter_batch_offset, iter_seed_offset=iter_seed_offset)
        return (iter_opts,)

```
