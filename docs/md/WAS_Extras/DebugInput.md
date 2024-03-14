# Debug Input
## Documentation
- Class name: `DebugInput`
- Category: `debug`
- Output node: `True`

The `DebugInput` node is designed for debugging purposes. It prints the input it receives and, if the input is an object that is not a basic data type (str, int, float, bool, list, dict, tuple), it also prints a directory listing of the object. This functionality can be particularly useful for inspecting the structure and properties of complex objects during the development and debugging of workflows.
## Input types
### Required
- **`input`**
    - The `input` parameter can accept any type of data, making this node highly versatile for debugging purposes. It is used to print the input data and, if applicable, the directory listing of the input object.
    - Python dtype: `AnyType`
    - Comfy dtype: `ANY`
## Output types
The node doesn't have output types
## Usage tips
- Infra type: `CPU`
- Common nodes: unknown

Often used for inspecting the structure and properties of complex objects during the development and debugging of workflows, the `DebugInput` node prints the input data it receives and, if applicable, provides a directory listing of the input object.
## Source code
```python
class WAS_DebugThis:
    def __init__(self):
        pass

    @classmethod
    def INPUT_TYPES(cls):
        return {
            "required": {"input": (wildcard, {})},
        }

    RETURN_TYPES = ()
    OUTPUT_NODE = True

    FUNCTION = "debug"

    CATEGORY = "debug"

    def debug(self, input):
    
        print("Debug:")
        print(input)
        if isinstance(input, object) and not isinstance(input, (str, int, float, bool, list, dict, tuple)):
            print("Objects directory listing:")
            pprint(dir(input), indent=4)
		
        return ()

```
