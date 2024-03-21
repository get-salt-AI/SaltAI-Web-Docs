# ImpactConditionalStopIteration
## Documentation
- Class name: `ImpactConditionalStopIteration`
- Category: `ImpactPack/Logic`
- Output node: `True`

This node provides a mechanism to conditionally stop the iteration of a process based on a boolean condition. If the condition is met, it sends a signal to stop the iteration.
## Input types
### Required
- **`cond`**
    - The boolean condition upon which the iteration may be stopped. Setting this to true triggers the stopping of the iteration.
    - Python dtype: `bool`
    - Comfy dtype: `BOOLEAN`
## Output types
The node doesn't have output types
## Usage tips
- Infra type: `CPU`
- Common nodes: unknown


## Source code
```python
class ImpactConditionalStopIteration:
    @classmethod
    def INPUT_TYPES(cls):
        return {
            "required": { "cond": ("BOOLEAN", {"forceInput": True}), },
        }

    FUNCTION = "doit"
    CATEGORY = "ImpactPack/Logic"

    RETURN_TYPES = ()

    OUTPUT_NODE = True

    def doit(self, cond):
        if cond:
            PromptServer.instance.send_sync("stop-iteration", {})
        return {}

```
