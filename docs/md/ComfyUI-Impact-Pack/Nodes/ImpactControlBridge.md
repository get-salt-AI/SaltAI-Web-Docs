# Control Bridge
## Documentation
- Class name: `ImpactControlBridge`
- Category: `ImpactPack/Logic/_for_test`
- Output node: `True`

The `ImpactControlBridge` node is designed to manage the state of other nodes based on specific conditions, such as muting, bypassing, or activating them. It communicates these state changes to the PromptServer, which then executes the necessary actions. This node plays a crucial role in controlling the flow and behavior of nodes within a system, especially in scenarios where dynamic adjustments are required.
## Input types
### Required
- **`value`**
    - unknown
    - Python dtype: `unknown`
    - Comfy dtype: `*`
- **`mode`**
    - Determines if the node should activate other nodes. When true, it activates nodes that are either muted or bypassed.
    - Python dtype: `bool`
    - Comfy dtype: `BOOLEAN`
- **`behavior`**
    - Controls the muting behavior of nodes. If true, it mutes nodes that are currently active or bypassed.
    - Python dtype: `bool`
    - Comfy dtype: `BOOLEAN`
## Output types
- **`*`**
    - Returns a tuple containing the original input value, indicating the successful execution of the node's logic. This output reflects the node's operation without altering the input's nature.
    - Python dtype: `Tuple[Any]`
    - Comfy dtype: `*`
## Usage tips
- Infra type: `CPU`
- Common nodes: unknown


## Source code
```python
class ImpactControlBridge:
    @classmethod
    def INPUT_TYPES(cls):
        return {"required": {
                      "value": (any_typ,),
                      "mode": ("BOOLEAN", {"default": True, "label_on": "Active", "label_off": "Mute/Bypass"}),
                      "behavior": ("BOOLEAN", {"default": True, "label_on": "Mute", "label_off": "Bypass"}),
                    },
                "hidden": {"unique_id": "UNIQUE_ID", "prompt": "PROMPT", "extra_pnginfo": "EXTRA_PNGINFO"}
                }

    FUNCTION = "doit"

    CATEGORY = "ImpactPack/Logic/_for_test"
    RETURN_TYPES = (any_typ,)
    RETURN_NAMES = ("value",)
    OUTPUT_NODE = True

    @classmethod
    def IS_CHANGED(self, value, mode, behavior=True, unique_id=None, prompt=None, extra_pnginfo=None):
        nodes, links = workflow_to_map(extra_pnginfo['workflow'])

        next_nodes = []

        for link in nodes[unique_id]['outputs'][0]['links']:
            node_id = str(links[link][2])
            impact.utils.collect_non_reroute_nodes(nodes, links, next_nodes, node_id)

        return next_nodes


    def doit(self, value, mode, behavior=True, unique_id=None, prompt=None, extra_pnginfo=None):
        global error_skip_flag

        nodes, links = workflow_to_map(extra_pnginfo['workflow'])

        active_nodes = []
        mute_nodes = []
        bypass_nodes = []

        for link in nodes[unique_id]['outputs'][0]['links']:
            node_id = str(links[link][2])

            next_nodes = []
            impact.utils.collect_non_reroute_nodes(nodes, links, next_nodes, node_id)

            for next_node_id in next_nodes:
                node_mode = nodes[next_node_id]['mode']

                if node_mode == 0:
                    active_nodes.append(next_node_id)
                elif node_mode == 2:
                    mute_nodes.append(next_node_id)
                elif node_mode == 4:
                    bypass_nodes.append(next_node_id)

        if mode:
            # active
            should_be_active_nodes = mute_nodes + bypass_nodes
            if len(should_be_active_nodes) > 0:
                PromptServer.instance.send_sync("impact-bridge-continue", {"node_id": unique_id, 'actives': list(should_be_active_nodes)})
                error_skip_flag = True
                raise Exception("IMPACT-PACK-SIGNAL: STOP CONTROL BRIDGE\nIf you see this message, your ComfyUI-Manager is outdated. Please update it.")

        elif behavior:
            # mute
            should_be_mute_nodes = active_nodes + bypass_nodes
            if len(should_be_mute_nodes) > 0:
                PromptServer.instance.send_sync("impact-bridge-continue", {"node_id": unique_id, 'mutes': list(should_be_mute_nodes)})
                error_skip_flag = True
                raise Exception("IMPACT-PACK-SIGNAL: STOP CONTROL BRIDGE\nIf you see this message, your ComfyUI-Manager is outdated. Please update it.")

        else:
            # bypass
            should_be_bypass_nodes = active_nodes + mute_nodes
            if len(should_be_bypass_nodes) > 0:
                PromptServer.instance.send_sync("impact-bridge-continue", {"node_id": unique_id, 'bypasses': list(should_be_bypass_nodes)})
                error_skip_flag = True
                raise Exception("IMPACT-PACK-SIGNAL: STOP CONTROL BRIDGE\nIf you see this message, your ComfyUI-Manager is outdated. Please update it.")

        return (value, )

```
