# Sleep
## Documentation
- Class name: `ImpactSleep`
- Category: `ImpactPack/Logic/_for_test`
- Output node: `True`

The `ImpactSleep` node introduces a delay in the execution flow by pausing for a specified number of seconds. This can be useful for timing operations or creating a pause in a sequence of actions.
## Input types
### Required
- **`signal`**
    - Acts as a pass-through for any type of signal, allowing the node to be integrated into various points in a workflow without altering the signal's content.
    - Python dtype: `Any`
    - Comfy dtype: `*`
- **`seconds`**
    - Specifies the duration of the pause in seconds. This allows for precise control over the timing of subsequent operations in the workflow.
    - Python dtype: `float`
    - Comfy dtype: `FLOAT`
## Output types
- **`*`**
    - Outputs the same signal that was input, effectively acting as a pass-through after the specified delay.
    - Python dtype: `Any`
    - Comfy dtype: `*`
## Usage tips
- Infra type: `CPU`
- Common nodes: unknown


## Source code
```python
class ImpactSleep:
    @classmethod
    def INPUT_TYPES(cls):
        return {"required": {
                    "signal": (any_typ,),
                    "seconds": ("FLOAT", {"default": 0.5, "min": 0, "max": 3600}),
                    }
                }

    FUNCTION = "doit"

    CATEGORY = "ImpactPack/Logic/_for_test"
    RETURN_TYPES = (any_typ,)
    RETURN_NAMES = ("signal_opt",)
    OUTPUT_NODE = True

    def doit(self, signal, seconds):
        time.sleep(seconds)
        return (signal,)

```
