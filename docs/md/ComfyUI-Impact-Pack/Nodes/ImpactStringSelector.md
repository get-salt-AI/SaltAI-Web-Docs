# String Selector
## Documentation
- Class name: `ImpactStringSelector`
- Category: `ImpactPack/Util`
- Output node: `False`

The `ImpactStringSelector` node allows for the selection of a specific string from a list of strings based on a given index. It supports both single-line and multi-line strings, with the ability to treat multi-line strings as separate entries. This node is useful for extracting specific pieces of text from a larger set, potentially for further processing or display.
## Input types
### Required
- **`strings`**
    - A list of strings from which a specific string is to be selected. Supports both single and multi-line strings, with special handling for multi-line strings when enabled.
    - Python dtype: `str`
    - Comfy dtype: `STRING`
- **`multiline`**
    - A boolean flag indicating whether multi-line strings should be treated as separate entries. When enabled, multi-line strings are split and processed individually.
    - Python dtype: `bool`
    - Comfy dtype: `BOOLEAN`
- **`select`**
    - The index of the string to be selected from the list. Supports dynamic selection through user input or programmatic control.
    - Python dtype: `int`
    - Comfy dtype: `INT`
## Output types
- **`string`**
    - The string selected based on the provided index. This output can be used for further processing or display.
    - Python dtype: `str`
    - Comfy dtype: `STRING`
## Usage tips
- Infra type: `CPU`
- Common nodes: unknown


## Source code
```python
class StringSelector:
    @classmethod
    def INPUT_TYPES(s):
        return {"required": {
            "strings": ("STRING", {"multiline": True}),
            "multiline": ("BOOLEAN", {"default": False, "label_on": "enabled", "label_off": "disabled"}),
            "select": ("INT", {"min": 0, "max": sys.maxsize, "step": 1, "default": 0}),
        }}

    RETURN_TYPES = ("STRING",)
    FUNCTION = "doit"

    CATEGORY = "ImpactPack/Util"

    def doit(self, strings, multiline, select):
        lines = strings.split('\n')

        if multiline:
            result = []
            current_string = ""

            for line in lines:
                if line.startswith("#"):
                    if current_string:
                        result.append(current_string.strip())
                        current_string = ""
                current_string += line + "\n"

            if current_string:
                result.append(current_string.strip())

            if len(result) == 0:
                selected = strings
            else:
                selected = result[select % len(result)]

            if selected.startswith('#'):
                selected = selected[1:]
        else:
            if len(lines) == 0:
                selected = strings
            else:
                selected = lines[select % len(lines)]

        return (selected, )

```
