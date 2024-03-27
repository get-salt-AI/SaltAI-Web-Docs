# Reroute Primitive 🐍
## Documentation
- Class name: `ReroutePrimitive|pysssss`
- Category: `utils`
- Output node: `False`

The ReroutePrimitive node is designed to pass through any given input without modification, acting as a universal connector in data flow architectures. It abstracts away the complexity of data types, allowing for flexible rerouting of data streams.
## Input types
### Required
- **`value`**
    - The 'value' parameter serves as the universal input that the node will pass through unchanged. It is central to the node's functionality, enabling it to act as a versatile connector in various data processing scenarios.
    - Comfy dtype: `*`
    - Python dtype: `AnyType`
## Output types
- **`*`**
    - Comfy dtype: `*`
    - The output is a tuple containing the unchanged input value, facilitating seamless data rerouting.
    - Python dtype: `Tuple[AnyType]`
## Usage tips
- Infra type: `CPU`
- Common nodes: `ImageUpscaleWithModel,CLIPTextEncodeSDXL,CLIPTextEncode,ColorCorrect,ImageCompositeMasked,ReroutePrimitive|pysssss,SaveImage`


## Source code
```python
class ReroutePrimitive:
    @classmethod
    def INPUT_TYPES(cls):
        return {
            "required": {"value": (any, )},
        }

    @classmethod
    def VALIDATE_INPUTS(s, **kwargs):
        return True

    RETURN_TYPES = (any,)
    FUNCTION = "route"
    CATEGORY = "utils"

    def route(self, value):
        return (value,)

```
