# DetailerHookCombine
## Documentation
- Class name: `DetailerHookCombine`
- Category: `ImpactPack/Upscale`
- Output node: `False`

The `DetailerHookCombine` node is designed to sequentially apply two detailer hooks to the input data. It operates on two main processes: `cycle_latent` and `post_detection`. In `cycle_latent`, it processes latent representations through both hooks in sequence, while in `post_detection`, it applies both hooks to segmentation data, also in sequence. This node effectively combines the functionalities of two detailer hooks, allowing for more complex data manipulation and enhancement.
## Input types
### Required
- **`hook1`**
    - The first detailer hook to be applied. It plays a crucial role in the initial processing or transformation of the input data.
    - Python dtype: `DetailerHook`
    - Comfy dtype: `DETAILER_HOOK`
- **`hook2`**
    - The second detailer hook to be applied after the first. It further processes or transforms the data, building upon the modifications made by the first hook.
    - Python dtype: `DetailerHook`
    - Comfy dtype: `DETAILER_HOOK`
## Output types
- **`detailer_hook`**
    - The combined detailer hook that encapsulates the sequential application of the two provided hooks.
    - Python dtype: `DetailerHook`
    - Comfy dtype: `DETAILER_HOOK`
## Usage tips
- Infra type: `CPU`
- Common nodes: unknown


## Source code
```python
class DetailerHookCombine:
    @classmethod
    def INPUT_TYPES(s):
        return {"required": {
                     "hook1": ("DETAILER_HOOK",),
                     "hook2": ("DETAILER_HOOK",),
                    },
                }

    RETURN_TYPES = ("DETAILER_HOOK",)
    FUNCTION = "doit"

    CATEGORY = "ImpactPack/Upscale"

    def doit(self, hook1, hook2):
        hook = hooks.DetailerHookCombine(hook1, hook2)
        return (hook, )

```
