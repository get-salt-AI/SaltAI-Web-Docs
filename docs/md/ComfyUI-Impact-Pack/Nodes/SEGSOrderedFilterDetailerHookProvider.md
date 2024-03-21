# SEGSOrderedFilterDetailerHookProvider
## Documentation
- Class name: `SEGSOrderedFilterDetailerHookProvider`
- Category: `ImpactPack/Util`
- Output node: `False`

This node provides a hook for filtering and ordering segmentation results based on specified criteria. It allows for the selection of segments based on their properties (e.g., area, width, height) and orders them according to a specified order (ascending or descending). It also supports taking a subset of the ordered segments starting from a specified index.
## Input types
### Required
- **`target`**
    - Specifies the property of the segments to be used for filtering and ordering. This can include properties like area, width, height, and coordinates.
    - Python dtype: `Tuple[str]`
    - Comfy dtype: `STRING`
- **`order`**
    - Determines the order in which the segments are sorted. A boolean value where True indicates descending order and False indicates ascending order.
    - Python dtype: `bool`
    - Comfy dtype: `BOOLEAN`
- **`take_start`**
    - The starting index from which to take the segments after ordering. Allows for selecting a subset of segments.
    - Python dtype: `int`
    - Comfy dtype: `INT`
- **`take_count`**
    - The number of segments to take starting from the 'take_start' index. This allows for limiting the number of segments to process.
    - Python dtype: `int`
    - Comfy dtype: `INT`
## Output types
- **`detailer_hook`**
    - Returns a detailer hook configured to filter and order segmentation results based on the specified criteria.
    - Python dtype: `hooks.SEGSOrderedFilterDetailerHook`
    - Comfy dtype: `DETAILER_HOOK`
## Usage tips
- Infra type: `CPU`
- Common nodes: unknown


## Source code
```python
class SEGSOrderedFilterDetailerHookProvider:
    @classmethod
    def INPUT_TYPES(s):
        return {"required": {
                        "target": (["area(=w*h)", "width", "height", "x1", "y1", "x2", "y2"],),
                        "order": ("BOOLEAN", {"default": True, "label_on": "descending", "label_off": "ascending"}),
                        "take_start": ("INT", {"default": 0, "min": 0, "max": sys.maxsize, "step": 1}),
                        "take_count": ("INT", {"default": 1, "min": 0, "max": sys.maxsize, "step": 1}),
                     },
                }

    RETURN_TYPES = ("DETAILER_HOOK", )
    FUNCTION = "doit"

    CATEGORY = "ImpactPack/Util"

    def doit(self, target, order, take_start, take_count):
        hook = hooks.SEGSOrderedFilterDetailerHook(target, order, take_start, take_count)
        return (hook, )

```
