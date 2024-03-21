# PixelKSampleHookCombine
## Documentation
- Class name: `PixelKSampleHookCombine`
- Category: `ImpactPack/Upscale`
- Output node: `False`

PixelKSampleHookCombine is a class designed to combine two PixelKSampleHook instances into a single hook. This allows for the sequential application of two separate hooks' functionalities on the same set of data, enhancing the flexibility and customization of the processing pipeline.
## Input types
### Required
- **`hook1`**
    - The first PixelKSampleHook instance to be combined. It serves as the initial processing step in the combined hook.
    - Python dtype: `PixelKSampleHook`
    - Comfy dtype: `PK_HOOK`
- **`hook2`**
    - The second PixelKSampleHook instance to be combined. It acts as the subsequent processing step, following the modifications made by the first hook.
    - Python dtype: `PixelKSampleHook`
    - Comfy dtype: `PK_HOOK`
## Output types
- **`pk_hook`**
    - The combined PixelKSampleHook instance, encapsulating the functionalities of both input hooks for sequential processing.
    - Python dtype: `PixelKSampleHookCombine`
    - Comfy dtype: `PK_HOOK`
## Usage tips
- Infra type: `CPU`
- Common nodes: unknown


## Source code
```python
class PixelKSampleHookCombine:
    @classmethod
    def INPUT_TYPES(s):
        return {"required": {
                     "hook1": ("PK_HOOK",),
                     "hook2": ("PK_HOOK",),
                    },
                }

    RETURN_TYPES = ("PK_HOOK",)
    FUNCTION = "doit"

    CATEGORY = "ImpactPack/Upscale"

    def doit(self, hook1, hook2):
        hook = hooks.PixelKSampleHookCombine(hook1, hook2)
        return (hook, )

```
