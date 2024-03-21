# SEGSRangeFilterDetailerHookProvider
## Documentation
- Class name: `SEGSRangeFilterDetailerHookProvider`
- Category: `ImpactPack/Util`
- Output node: `False`

The SEGSRangeFilterDetailerHookProvider node is designed to create a detailer hook that filters segments based on a range criteria. It configures the filtering process by specifying a target attribute, a mode to determine if the filtering should include or exclude segments within the specified range, and the minimum and maximum values defining the range. This allows for dynamic segmentation based on attributes like area, width, height, or coordinates, enhancing the flexibility and precision of segment processing.
## Input types
### Required
- **`target`**
    - Specifies the attribute of the segments to be filtered, such as area, width, height, or coordinates. This parameter determines the basis for the range filtering, allowing for versatile segment selection based on geometric properties.
    - Python dtype: `Tuple[str]`
    - Comfy dtype: `['STRING']`
- **`mode`**
    - Determines the filtering mode, either including or excluding segments within the specified range. This boolean parameter enhances control over the filtering process, allowing for both inclusive and exclusive segment selection.
    - Python dtype: `bool`
    - Comfy dtype: `BOOLEAN`
- **`min_value`**
    - Defines the minimum value of the range for filtering. Segments with the specified attribute below this value can be included or excluded based on the mode, enabling precise lower-bound control.
    - Python dtype: `int`
    - Comfy dtype: `INT`
- **`max_value`**
    - Sets the maximum value of the range for filtering. This parameter allows for upper-bound specification, ensuring segments with the target attribute above this value can be selectively processed.
    - Python dtype: `int`
    - Comfy dtype: `INT`
## Output types
- **`detailer_hook`**
    - Produces a detailer hook configured for range-based segment filtering. This output enables the integration of the filtering logic into the segment processing workflow, facilitating targeted segment manipulation.
    - Python dtype: `hooks.SEGSRangeFilterDetailerHook`
    - Comfy dtype: `DETAILER_HOOK`
## Usage tips
- Infra type: `CPU`
- Common nodes: unknown


## Source code
```python
class SEGSRangeFilterDetailerHookProvider:
    @classmethod
    def INPUT_TYPES(s):
        return {"required": {
                        "target": (["area(=w*h)", "width", "height", "x1", "y1", "x2", "y2", "length_percent"],),
                        "mode": ("BOOLEAN", {"default": True, "label_on": "inside", "label_off": "outside"}),
                        "min_value": ("INT", {"default": 0, "min": 0, "max": sys.maxsize, "step": 1}),
                        "max_value": ("INT", {"default": 67108864, "min": 0, "max": sys.maxsize, "step": 1}),
                     },
                }

    RETURN_TYPES = ("DETAILER_HOOK", )
    FUNCTION = "doit"

    CATEGORY = "ImpactPack/Util"

    def doit(self, target, mode, min_value, max_value):
        hook = hooks.SEGSRangeFilterDetailerHook(target, mode, min_value, max_value)
        return (hook, )

```
