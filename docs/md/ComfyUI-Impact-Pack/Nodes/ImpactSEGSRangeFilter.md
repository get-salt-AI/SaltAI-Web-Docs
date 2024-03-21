# SEGS Filter (range)
## Documentation
- Class name: `ImpactSEGSRangeFilter`
- Category: `ImpactPack/Util`
- Output node: `False`

The ImpactSEGSRangeFilter node filters segments based on specified criteria related to their geometric properties, such as area, width, height, or position. It allows for the inclusion or exclusion of segments within a certain range of values, enhancing the ability to focus on segments of interest within an image.
## Input types
### Required
- **`segs`**
    - The input segments to be filtered. This parameter is crucial as it determines the set of segments that will be subjected to the filtering criteria.
    - Python dtype: `Tuple[str, List[SEG]]`
    - Comfy dtype: `SEGS`
- **`target`**
    - Specifies the geometric property (e.g., area, width, height, position) to be used as the basis for filtering. This choice directly influences which segments are included or excluded based on the specified range.
    - Python dtype: `str`
    - Comfy dtype: `STRING`
- **`mode`**
    - Determines whether segments inside or outside the specified range should be included. This parameter affects the filtering logic, allowing for more flexible segment selection.
    - Python dtype: `bool`
    - Comfy dtype: `BOOLEAN`
- **`min_value`**
    - The minimum value of the specified geometric property for a segment to be included or excluded, depending on the mode. It sets the lower bound of the filtering range.
    - Python dtype: `int`
    - Comfy dtype: `INT`
- **`max_value`**
    - The maximum value of the specified geometric property for a segment to be included or excluded, depending on the mode. It sets the upper bound of the filtering range.
    - Python dtype: `int`
    - Comfy dtype: `INT`
## Output types
- **`segs`**
    - The filtered segments after applying the specified geometric property criteria. This output allows for further processing or analysis of the segments of interest.
    - Python dtype: `Tuple[str, List[SEG]]`
    - Comfy dtype: `SEGS`
## Usage tips
- Infra type: `CPU`
- Common nodes: unknown


## Source code
```python
class SEGSRangeFilter:
    @classmethod
    def INPUT_TYPES(s):
        return {"required": {
                        "segs": ("SEGS", ),
                        "target": (["area(=w*h)", "width", "height", "x1", "y1", "x2", "y2", "length_percent"],),
                        "mode": ("BOOLEAN", {"default": True, "label_on": "inside", "label_off": "outside"}),
                        "min_value": ("INT", {"default": 0, "min": 0, "max": sys.maxsize, "step": 1}),
                        "max_value": ("INT", {"default": 67108864, "min": 0, "max": sys.maxsize, "step": 1}),
                     },
                }

    RETURN_TYPES = ("SEGS", "SEGS",)
    RETURN_NAMES = ("filtered_SEGS", "remained_SEGS",)
    FUNCTION = "doit"

    CATEGORY = "ImpactPack/Util"

    def doit(self, segs, target, mode, min_value, max_value):
        new_segs = []
        remained_segs = []

        for seg in segs[1]:
            x1 = seg.crop_region[0]
            y1 = seg.crop_region[1]
            x2 = seg.crop_region[2]
            y2 = seg.crop_region[3]

            if target == "area(=w*h)":
                value = (y2 - y1) * (x2 - x1)
            elif target == "length_percent":
                h = y2 - y1
                w = x2 - x1
                value = max(h/w, w/h)*100
                print(f"value={value}")
            elif target == "width":
                value = x2 - x1
            elif target == "height":
                value = y2 - y1
            elif target == "x1":
                value = x1
            elif target == "x2":
                value = x2
            elif target == "y1":
                value = y1
            else:
                value = y2

            if mode and min_value <= value <= max_value:
                print(f"[in] value={value} / {mode}, {min_value}, {max_value}")
                new_segs.append(seg)
            elif not mode and (value < min_value or value > max_value):
                print(f"[out] value={value} / {mode}, {min_value}, {max_value}")
                new_segs.append(seg)
            else:
                remained_segs.append(seg)
                print(f"[filter] value={value} / {mode}, {min_value}, {max_value}")

        return ((segs[0], new_segs), (segs[0], remained_segs), )

```
