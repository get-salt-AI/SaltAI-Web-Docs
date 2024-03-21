# Queue Trigger (Countdown)
## Documentation
- Class name: `ImpactQueueTriggerCountdown`
- Category: `ImpactPack/Logic/_for_test`
- Output node: `True`

The `ImpactQueueTriggerCountdown` node is designed to manage a countdown process within a queue system. It increments a count towards a total and triggers specific actions based on the count's value and a mode setting. When the count is less than the total minus one and the mode is active, it sends a signal to increment the count and potentially add to the queue. If the count reaches or exceeds the total minus one, it resets the count to zero.
## Input types
### Required
- **`signal`**
    - The `signal` input acts as a pass-through for any type of data, allowing this node to be integrated into various points of a workflow without altering the data flow.
    - Python dtype: `typing.Any`
    - Comfy dtype: `*`
- **`count`**
    - The `count` input tracks the current count in the countdown process. It is crucial for determining the node's actions, such as whether to increment the count or reset it.
    - Python dtype: `int`
    - Comfy dtype: `INT`
- **`total`**
    - The `total` input specifies the target count for the countdown. It defines the endpoint of the countdown process.
    - Python dtype: `int`
    - Comfy dtype: `INT`
- **`mode`**
    - The `mode` input determines whether the countdown process is active. When true, the node will perform its countdown logic; otherwise, it remains inactive.
    - Python dtype: `bool`
    - Comfy dtype: `BOOLEAN`
## Output types
- **`*`**
    - The `signal_opt` output passes through the input signal, allowing for seamless integration into the workflow.
    - Python dtype: `typing.Any`
    - Comfy dtype: `*`
- **`int`**
    - The `total` output passes through the input total, maintaining consistency in the workflow.
    - Python dtype: `int`
    - Comfy dtype: `INT`
- **`ui`**
    - The `ui` parameter is not explicitly mentioned in the provided context, indicating that this node does not directly manipulate or generate UI elements.
## Usage tips
- Infra type: `CPU`
- Common nodes: unknown


## Source code
```python
class ImpactQueueTriggerCountdown:
    @classmethod
    def INPUT_TYPES(cls):
        return {"required": {
                    "signal": (any_typ,),
                    "count": ("INT", {"default": 0, "min": 0, "max": 0xffffffffffffffff}),
                    "total": ("INT", {"default": 10, "min": 1, "max": 0xffffffffffffffff}),
                    "mode": ("BOOLEAN", {"default": True, "label_on": "Trigger", "label_off": "Don't trigger"}),
                    },
                "hidden": {"unique_id": "UNIQUE_ID"}
                }

    FUNCTION = "doit"

    CATEGORY = "ImpactPack/Logic/_for_test"
    RETURN_TYPES = (any_typ, "INT", "INT")
    RETURN_NAMES = ("signal_opt", "count", "total")
    OUTPUT_NODE = True

    def doit(self, signal, count, total, mode, unique_id):
        if count < total - 1 and (mode):
            PromptServer.instance.send_sync("impact-node-feedback",
                                            {"node_id": unique_id, "widget_name": "count", "type": "int", "value": count+1})
            PromptServer.instance.send_sync("impact-add-queue", {})
        if count >= total - 1:
            PromptServer.instance.send_sync("impact-node-feedback",
                                            {"node_id": unique_id, "widget_name": "count", "type": "int", "value": 0})

        return (signal, count, total)

```
