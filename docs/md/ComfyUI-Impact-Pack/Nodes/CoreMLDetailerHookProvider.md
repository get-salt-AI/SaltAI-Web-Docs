# CoreMLDetailerHookProvider
## Documentation
- Class name: `CoreMLDetailerHookProvider`
- Category: `ImpactPack/Detailer`
- Output node: `False`

This node provides a hook for CoreML models, allowing for the customization of the model's behavior based on the specified mode. It is part of the ImpactPack/Detailer category, designed to enhance or modify the processing of images or data in a detail-oriented manner.
## Input types
### Required
- **`mode`**
    - The mode parameter specifies the resolution at which the CoreML model operates. It affects the model's processing capabilities and output quality, allowing for flexibility in handling different image sizes.
    - Python dtype: `Tuple[List[str]]`
    - Comfy dtype: `['512x512', '768x768', '512x768', '768x512']`
## Output types
- **`detailer_hook`**
    - Returns a CoreML hook configured with the specified mode, ready to be used for detailed processing or modification of data.
    - Python dtype: `Tuple[CoreMLHook]`
    - Comfy dtype: `DETAILER_HOOK`
## Usage tips
- Infra type: `CPU`
- Common nodes: unknown


## Source code
```python
class CoreMLDetailerHookProvider:
    @classmethod
    def INPUT_TYPES(s):
        return {"required": {"mode": (["512x512", "768x768", "512x768", "768x512"], )}, }

    RETURN_TYPES = ("DETAILER_HOOK",)
    FUNCTION = "doit"

    CATEGORY = "ImpactPack/Detailer"

    def doit(self, mode):
        hook = hooks.CoreMLHook(mode)
        return (hook, )

```
