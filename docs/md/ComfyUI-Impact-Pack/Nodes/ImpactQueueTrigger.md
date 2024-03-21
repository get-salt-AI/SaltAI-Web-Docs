# Queue Trigger
## Documentation
- Class name: `ImpactQueueTrigger`
- Category: `ImpactPack/Logic/_for_test`
- Output node: `True`

The `ImpactQueueTrigger` node is designed to conditionally trigger an action based on a boolean mode. If the mode is true, it sends a synchronous request to add a task to a queue, effectively acting as a conditional trigger for subsequent operations.
## Input types
### Required
- **`signal`**
    - Acts as a pass-through for any type of signal, allowing the node to be integrated into various workflows without altering the signal's nature. Its primary role is to facilitate the conditional execution based on the 'mode' parameter.
    - Python dtype: `typing.Any`
    - Comfy dtype: `*`
- **`mode`**
    - Determines whether the node should trigger the action. When true, it triggers an addition to a queue, serving as a conditional execution gate. This boolean input directly controls whether the subsequent operation in the queue is executed, making it a critical factor in the node's functionality.
    - Python dtype: `bool`
    - Comfy dtype: `BOOLEAN`
## Output types
- **`*`**
    - Outputs the same signal received as input, unaffected by the node's operation, ensuring seamless integration into the workflow. The presence of this output parameter named 'signal_opt' indicates that the node returns the input signal explicitly, maintaining the flow of data through the node.
    - Python dtype: `typing.Any`
    - Comfy dtype: `*`
## Usage tips
- Infra type: `CPU`
- Common nodes: unknown


## Source code
```python
class ImpactQueueTrigger:
    @classmethod
    def INPUT_TYPES(cls):
        return {"required": {
                    "signal": (any_typ,),
                    "mode": ("BOOLEAN", {"default": True, "label_on": "Trigger", "label_off": "Don't trigger"}),
                    }
                }

    FUNCTION = "doit"

    CATEGORY = "ImpactPack/Logic/_for_test"
    RETURN_TYPES = (any_typ,)
    RETURN_NAMES = ("signal_opt",)
    OUTPUT_NODE = True

    def doit(self, signal, mode):
        if(mode):
            PromptServer.instance.send_sync("impact-add-queue", {})

        return (signal,)

```
