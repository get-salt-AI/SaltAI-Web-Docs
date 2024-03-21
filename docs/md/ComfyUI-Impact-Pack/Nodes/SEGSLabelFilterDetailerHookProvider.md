# SEGSLabelFilterDetailerHookProvider
## Documentation
- Class name: `SEGSLabelFilterDetailerHookProvider`
- Category: `ImpactPack/Util`
- Output node: `False`

The SEGSLabelFilterDetailerHookProvider node is designed to create a detailer hook that filters segments based on specified labels. It allows for the selection of segments to be included or excluded in the processing pipeline based on their labels, enhancing the flexibility and specificity of segment handling.
## Input types
### Required
- **`segs`**
    - Specifies the segments to be filtered. This parameter is crucial for determining which segments are subject to filtering based on the provided labels.
    - Python dtype: `SEGS`
    - Comfy dtype: `SEGS`
- **`preset`**
    - Defines a preset condition for the filter, allowing for a predefined set of labels to be used as a basis for filtering. This parameter offers a convenient way to apply common filtering criteria without specifying individual labels.
    - Python dtype: `Union[str, List[str]]`
    - Comfy dtype: `STRING`
- **`labels`**
    - Lists the specific types of segments to be allowed through the filter, separated by commas. This parameter directly influences the filtering process by specifying which labels are considered for inclusion or exclusion.
    - Python dtype: `str`
    - Comfy dtype: `STRING`
## Output types
- **`detailer_hook`**
    - Produces a detailer hook configured to filter segments based on the specified labels. This output is essential for integrating the label-based filtering functionality into the processing pipeline.
    - Python dtype: `SEGSLabelFilterDetailerHook`
    - Comfy dtype: `DETAILER_HOOK`
## Usage tips
- Infra type: `CPU`
- Common nodes: unknown


## Source code
```python
class SEGSLabelFilterDetailerHookProvider:
    @classmethod
    def INPUT_TYPES(s):
        return {"required": {
                        "segs": ("SEGS", ),
                        "preset": (['all'] + defs.detection_labels,),
                        "labels": ("STRING", {"multiline": True, "placeholder": "List the types of segments to be allowed, separated by commas"}),
                     },
                }

    RETURN_TYPES = ("DETAILER_HOOK", )
    FUNCTION = "doit"

    CATEGORY = "ImpactPack/Util"

    def doit(self, preset, labels):
        hook = hooks.SEGSLabelFilterDetailerHook(labels)
        return (hook, )

```
