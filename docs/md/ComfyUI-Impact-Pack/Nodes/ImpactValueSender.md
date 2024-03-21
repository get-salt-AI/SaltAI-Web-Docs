# ImpactValueSender
## Documentation
- Class name: `ImpactValueSender`
- Category: `ImpactPack/Logic`
- Output node: `True`

The `ImpactValueSender` node is designed to transmit a value along with a link identifier to a specified destination. It primarily serves in scenarios where data needs to be sent synchronously across different parts of a system, ensuring that the value is delivered accurately and efficiently.
## Input types
### Required
- **`value`**
    - This parameter represents the data to be sent. Its flexibility in type allows for a wide range of data to be transmitted, making the node versatile in its application.
    - Python dtype: `Any`
    - Comfy dtype: `*`
- **`link_id`**
    - The link identifier specifies the destination or channel through which the value should be sent. It plays a crucial role in ensuring that the data reaches the correct recipient.
    - Python dtype: `int`
    - Comfy dtype: `INT`
### Optional
- **`signal_opt`**
    - An optional signal that can be sent along with the value. This allows for additional context or commands to be transmitted, enhancing the node's functionality.
    - Python dtype: `Any`
    - Comfy dtype: `*`
## Output types
- **`*`**
    - Returns the optional signal if provided, allowing for further actions based on this signal.
    - Python dtype: `Any`
    - Comfy dtype: `*`
## Usage tips
- Infra type: `CPU`
- Common nodes: unknown


## Source code
```python
class ImpactValueSender:
    @classmethod
    def INPUT_TYPES(cls):
        return {"required": {
                    "value": (any_typ, ),
                    "link_id": ("INT", {"default": 0, "min": 0, "max": sys.maxsize, "step": 1}),
                    },
                "optional": {
                        "signal_opt": (any_typ,),
                    }
                }

    OUTPUT_NODE = True

    FUNCTION = "doit"

    CATEGORY = "ImpactPack/Logic"

    RETURN_TYPES = (any_typ, )
    RETURN_NAMES = ("signal", )

    def doit(self, value, link_id=0, signal_opt=None):
        PromptServer.instance.send_sync("value-send", {"link_id": link_id, "value": value})
        return (signal_opt, )

```
