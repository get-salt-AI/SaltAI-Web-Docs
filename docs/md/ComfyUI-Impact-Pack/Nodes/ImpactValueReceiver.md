# ImpactValueReceiver
## Documentation
- Class name: `ImpactValueReceiver`
- Category: `ImpactPack/Logic`
- Output node: `False`

The `ImpactValueReceiver` node is designed to receive values and convert them into specific data types based on the input type specified. It supports conversion to integer, float, boolean, and string types, making it versatile for handling various data inputs.
## Input types
### Required
- **`typ`**
    - Specifies the data type to which the input value should be converted. It supports string, integer, float, and boolean types, affecting the output data type accordingly.
    - Python dtype: `str`
    - Comfy dtype: `['STRING', 'INT', 'FLOAT', 'BOOLEAN']`
- **`value`**
    - The value to be converted to the specified type. Its initial type is string, and it's transformed based on the 'typ' parameter.
    - Python dtype: `str`
    - Comfy dtype: `STRING`
- **`link_id`**
    - An identifier for linking this node's operation within a larger workflow. It doesn't affect the conversion process directly.
    - Python dtype: `int`
    - Comfy dtype: `INT`
## Output types
- **`*`**
    - The output signal, which is the input value converted to the specified data type.
    - Python dtype: `Union[int, float, bool, str]`
    - Comfy dtype: `*`
## Usage tips
- Infra type: `CPU`
- Common nodes: unknown


## Source code
```python
class ImpactValueReceiver:
    @classmethod
    def INPUT_TYPES(cls):
        return {"required": {
                    "typ": (["STRING", "INT", "FLOAT", "BOOLEAN"], ),
                    "value": ("STRING", {"default": ""}),
                    "link_id": ("INT", {"default": 0, "min": 0, "max": sys.maxsize, "step": 1}),
                    },
                }

    FUNCTION = "doit"

    CATEGORY = "ImpactPack/Logic"

    RETURN_TYPES = (any_typ, )

    def doit(self, typ, value, link_id=0):
        if typ == "INT":
            return (int(value), )
        elif typ == "FLOAT":
            return (float(value), )
        elif typ == "BOOLEAN":
            return (value.lower() == "true", )
        else:
            return (value, )

```
