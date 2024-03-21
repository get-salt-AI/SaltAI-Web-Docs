# ImpactLogger
## Documentation
- Class name: `ImpactLogger`
- Category: `ImpactPack/Debug`
- Output node: `True`

The `ImpactLogger` node is designed for debugging purposes within the ImpactPack. It logs the shape and content of the input data, along with a specified prompt and additional PNG information, to the console. This functionality aids in understanding the flow of data and the execution of workflows, especially in complex scenarios.
## Input types
### Required
- **`data`**
    - The primary data that will be logged. This can be of any type and is crucial for understanding the content and structure of the data flowing through the node.
    - Python dtype: `Any`
    - Comfy dtype: `*`
## Output types
The node doesn't have output types
## Usage tips
- Infra type: `CPU`
- Common nodes: unknown


## Source code
```python
class ImpactLogger:
    @classmethod
    def INPUT_TYPES(s):
        return {"required": {
                        "data": (any_typ, ""),
                    },
                "hidden": {"prompt": "PROMPT", "extra_pnginfo": "EXTRA_PNGINFO"},
                }

    CATEGORY = "ImpactPack/Debug"

    OUTPUT_NODE = True

    RETURN_TYPES = ()
    FUNCTION = "doit"

    def doit(self, data, prompt, extra_pnginfo):
        shape = ""
        if hasattr(data, "shape"):
            shape = f"{data.shape} / "

        print(f"[IMPACT LOGGER]: {shape}{data}")

        print(f"         PROMPT: {prompt}")

        # for x in prompt:
        #     if 'inputs' in x and 'populated_text' in x['inputs']:
        #         print(f"PROMP: {x['10']['inputs']['populated_text']}")
        #
        # for x in extra_pnginfo['workflow']['nodes']:
        #     if x['type'] == 'ImpactWildcardProcessor':
        #         print(f" WV : {x['widgets_values'][1]}\n")

        return {}

```
