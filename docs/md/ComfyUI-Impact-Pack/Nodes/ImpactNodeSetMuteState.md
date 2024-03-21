# Set Mute State
## Documentation
- Class name: `ImpactNodeSetMuteState`
- Category: `ImpactPack/Logic/_for_test`
- Output node: `True`

This node is responsible for setting the mute state of a specified node within the system. It communicates with a server to update the mute state of the node based on the provided parameters.
## Input types
### Required
- **`signal`**
    - Acts as a pass-through for signals within the system, allowing for the mute state setting operation to be integrated into a sequence of operations without disrupting the flow of data.
    - Python dtype: `typing.Any`
    - Comfy dtype: `*`
- **`node_id`**
    - Identifies the specific node whose mute state is to be set, allowing for targeted control within a larger system.
    - Python dtype: `int`
    - Comfy dtype: `INT`
- **`set_state`**
    - Determines the new mute state of the node, with options to either activate or mute the node, thereby controlling its participation in the system's operations.
    - Python dtype: `bool`
    - Comfy dtype: `BOOLEAN`
## Output types
- **`*`**
    - Returns the original signal, ensuring continuity in the flow of data through the system after the mute state has been set.
    - Python dtype: `typing.Any`
    - Comfy dtype: `*`
## Usage tips
- Infra type: `CPU`
- Common nodes: unknown


## Source code
```python
class ImpactNodeSetMuteState:
    @classmethod
    def INPUT_TYPES(cls):
        return {"required": {
                    "signal": (any_typ,),
                    "node_id": ("INT", {"default": 0, "min": 0, "max": 0xffffffffffffffff}),
                    "set_state": ("BOOLEAN", {"default": True, "label_on": "active", "label_off": "mute"}),
                    }
                }

    FUNCTION = "doit"

    CATEGORY = "ImpactPack/Logic/_for_test"
    RETURN_TYPES = (any_typ,)
    RETURN_NAMES = ("signal_opt",)
    OUTPUT_NODE = True

    def doit(self, signal, node_id, set_state):
        PromptServer.instance.send_sync("impact-node-mute-state", {"node_id": node_id, "is_active": set_state})
        return (signal,)

```
