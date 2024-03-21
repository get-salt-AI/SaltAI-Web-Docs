# SEGS Filter (ordered)
## Documentation
- Class name: `ImpactSEGSOrderedFilter`
- Category: `ImpactPack/Util`
- Output node: `False`

This node applies an ordered filtering operation on segmentation data based on specified criteria. It allows for sorting and selecting a subset of the segmentation data according to the defined order and range.
## Input types
### Required
- **`segs`**
    - The segmentation data to be filtered and ordered.
    - Python dtype: `List[SEG]`
    - Comfy dtype: `SEGS`
- **`target`**
    - Specifies the criterion used for ordering the segmentation data, such as area, width, or position.
    - Python dtype: `Union[str, List[str]]`
    - Comfy dtype: `STRING`
- **`order`**
    - Determines the sorting order of the segmentation data, where a boolean value indicates descending or ascending order.
    - Python dtype: `bool`
    - Comfy dtype: `BOOLEAN`
- **`take_start`**
    - Defines the starting index from which to take the segmentation data after ordering, allowing for selection of a specific range.
    - Python dtype: `int`
    - Comfy dtype: `INT`
- **`take_count`**
    - Specifies the number of segmentation data items to take from the starting index, enabling control over the subset size.
    - Python dtype: `int`
    - Comfy dtype: `INT`
## Output types
- **`segs`**
    - Produces the filtered and ordered segmentation data.
    - Python dtype: `List[SEG]`
    - Comfy dtype: `SEGS`
## Usage tips
- Infra type: `CPU`
- Common nodes: `ImpactDilateMaskInSEGS,DetailerForEachDebug`


## Source code
```python
class SEGSOrderedFilter:
    @classmethod
    def INPUT_TYPES(s):
        return {"required": {
                        "segs": ("SEGS", ),
                        "target": (["area(=w*h)", "width", "height", "x1", "y1", "x2", "y2"],),
                        "order": ("BOOLEAN", {"default": True, "label_on": "descending", "label_off": "ascending"}),
                        "take_start": ("INT", {"default": 0, "min": 0, "max": sys.maxsize, "step": 1}),
                        "take_count": ("INT", {"default": 1, "min": 0, "max": sys.maxsize, "step": 1}),
                     },
                }

    RETURN_TYPES = ("SEGS", "SEGS",)
    RETURN_NAMES = ("filtered_SEGS", "remained_SEGS",)
    FUNCTION = "doit"

    CATEGORY = "ImpactPack/Util"

    def doit(self, segs, target, order, take_start, take_count):
        segs_with_order = []

        for seg in segs[1]:
            x1 = seg.crop_region[0]
            y1 = seg.crop_region[1]
            x2 = seg.crop_region[2]
            y2 = seg.crop_region[3]

            if target == "area(=w*h)":
                value = (y2 - y1) * (x2 - x1)
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

            segs_with_order.append((value, seg))

        if order:
            sorted_list = sorted(segs_with_order, key=lambda x: x[0], reverse=True)
        else:
            sorted_list = sorted(segs_with_order, key=lambda x: x[0], reverse=False)

        result_list = []
        remained_list = []

        for i, item in enumerate(sorted_list):
            if take_start <= i < take_start + take_count:
                result_list.append(item[1])
            else:
                remained_list.append(item[1])

        return ((segs[0], result_list), (segs[0], remained_list), )

```
