# SEGS Filter (label)
## Documentation
- Class name: `ImpactSEGSLabelFilter`
- Category: `ImpactPack/Util`
- Output node: `False`

The `ImpactSEGSLabelFilter` node filters segments (SEGS) based on specified labels. It allows for the inclusion or exclusion of segments by matching their labels against a provided list, supporting special groupings like 'eyes', 'eyebrows', and 'pupils' for more granular control.
## Input types
### Required
- **`segs`**
    - The input segments to be filtered. This parameter is crucial as it provides the data on which the filtering operation is performed.
    - Python dtype: `Tuple[Tuple[int, int], List[Segment]]`
    - Comfy dtype: `SEGS`
- **`preset`**
    - A preset option that can include 'all' or specific detection labels. It influences the filtering process by pre-defining a set of labels to be included or excluded.
    - Python dtype: `List[str]`
    - Comfy dtype: `STRING`
- **`labels`**
    - A comma-separated list of labels specifying which segments to allow through the filter. This parameter directly determines the outcome of the filtering process.
    - Python dtype: `str`
    - Comfy dtype: `STRING`
## Output types
- **`segs`**
    - The segments that did not match the specified labels and were therefore excluded by the filter.
    - Python dtype: `Tuple[Tuple[int, int], List[Segment]]`
    - Comfy dtype: `SEGS`
## Usage tips
- Infra type: `CPU`
- Common nodes: unknown


## Source code
```python
class SEGSLabelFilter:
    @classmethod
    def INPUT_TYPES(s):
        return {"required": {
                        "segs": ("SEGS", ),
                        "preset": (['all'] + defs.detection_labels, ),
                        "labels": ("STRING", {"multiline": True, "placeholder": "List the types of segments to be allowed, separated by commas"}),
                     },
                }

    RETURN_TYPES = ("SEGS", "SEGS",)
    RETURN_NAMES = ("filtered_SEGS", "remained_SEGS",)
    FUNCTION = "doit"

    CATEGORY = "ImpactPack/Util"

    @staticmethod
    def filter(segs, labels):
        labels = set([label.strip() for label in labels])

        if 'all' in labels:
            return (segs, (segs[0], []), )
        else:
            res_segs = []
            remained_segs = []

            for x in segs[1]:
                if x.label in labels:
                    res_segs.append(x)
                elif 'eyes' in labels and x.label in ['left_eye', 'right_eye']:
                    res_segs.append(x)
                elif 'eyebrows' in labels and x.label in ['left_eyebrow', 'right_eyebrow']:
                    res_segs.append(x)
                elif 'pupils' in labels and x.label in ['left_pupil', 'right_pupil']:
                    res_segs.append(x)
                else:
                    remained_segs.append(x)

        return ((segs[0], res_segs), (segs[0], remained_segs), )

    def doit(self, segs, preset, labels):
        labels = labels.split(',')
        return SEGSLabelFilter.filter(segs, labels)

```
