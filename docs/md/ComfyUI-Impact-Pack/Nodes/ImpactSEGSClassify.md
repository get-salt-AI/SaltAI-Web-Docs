# SEGS Classify
## Documentation
- Class name: `ImpactSEGSClassify`
- Category: `ImpactPack/HuggingFace`
- Output node: `False`

This node classifies segments (SEGS) based on the comparison of labels or numeric values. It filters and divides the segments into two groups: those that meet the specified condition (filtered_SEGS) and those that do not (remained_SEGS), based on the comparison operation (e.g., '>', '<', '>=', '<=', '==') between the values or labels assigned to each segment.
## Input types
### Required
- **`classifier`**
    - The classification model used to classify each segment. It's essential for determining the classification results which are then used to filter segments based on the comparison condition.
    - Python dtype: `torch.nn.Module`
    - Comfy dtype: `TRANSFORMERS_CLASSIFIER`
- **`segs`**
    - The segments to be classified and filtered. This input is crucial for the node's operation as it provides the data that will be processed and divided based on the specified conditions.
    - Python dtype: `List[Tuple[Any, Any]]`
    - Comfy dtype: `SEGS`
- **`preset_expr`**
    - A predefined expression or a manual expression for comparison. It defines the basis for comparing segment labels or values, playing a critical role in how segments are filtered.
    - Python dtype: `str`
    - Comfy dtype: `STRING`
- **`manual_expr`**
    - A manually entered expression for comparison, used when 'preset_expr' is set to 'Manual expr'. It allows for custom comparison conditions to be defined.
    - Python dtype: `str`
    - Comfy dtype: `STRING`
### Optional
- **`ref_image_opt`**
    - An optional reference image that can be used for additional context or processing. It provides flexibility in how segments are handled and classified.
    - Python dtype: `Optional[torch.Tensor]`
    - Comfy dtype: `IMAGE`
## Output types
- **`segs`**
    - Segments that do not meet the specified comparison condition.
    - Python dtype: `List[Any]`
    - Comfy dtype: `SEGS`
## Usage tips
- Infra type: `CPU`
- Common nodes: unknown


## Source code
```python
class SEGS_Classify:
    @classmethod
    def INPUT_TYPES(s):
        global preset_classify_expr
        return {"required": {
                        "classifier": ("TRANSFORMERS_CLASSIFIER",),
                        "segs": ("SEGS",),
                        "preset_expr": (preset_classify_expr + ['Manual expr'],),
                        "manual_expr": ("STRING", {"multiline": False}),
                     },
                "optional": {
                     "ref_image_opt": ("IMAGE", ),
                    }
                }

    RETURN_TYPES = ("SEGS", "SEGS",)
    RETURN_NAMES = ("filtered_SEGS", "remained_SEGS",)

    FUNCTION = "doit"

    CATEGORY = "ImpactPack/HuggingFace"

    @staticmethod
    def lookup_classified_label_score(score_infos, label):
        global symbolic_label_map

        if label.startswith('#'):
            if label not in symbolic_label_map:
                return None
            else:
                label = symbolic_label_map[label]
        else:
            label = {label}

        for x in score_infos:
            if x['label'] in label:
                return x['score']

        return None

    def doit(self, classifier, segs, preset_expr, manual_expr, ref_image_opt=None):
        if preset_expr == 'Manual expr':
            expr_str = manual_expr
        else:
            expr_str = preset_expr

        match = re.match(classify_expr_pattern, expr_str)

        if match is None:
            return ((segs[0], []), segs)

        a = match.group(1)
        op = match.group(2)
        b = match.group(3)

        a_is_lab = not is_numeric_string(a)
        b_is_lab = not is_numeric_string(b)

        classified = []
        remained_SEGS = []

        for seg in segs[1]:
            cropped_image = None

            if seg.cropped_image is not None:
                cropped_image = seg.cropped_image
            elif ref_image_opt is not None:
                # take from original image
                cropped_image = crop_image(ref_image_opt, seg.crop_region)

            if cropped_image is not None:
                cropped_image = to_pil(cropped_image)
                res = classifier(cropped_image)
                classified.append((seg, res))
            else:
                remained_SEGS.append(seg)

        filtered_SEGS = []
        for seg, res in classified:
            if a_is_lab:
                avalue = SEGS_Classify.lookup_classified_label_score(res, a)
            else:
                avalue = a

            if b_is_lab:
                bvalue = SEGS_Classify.lookup_classified_label_score(res, b)
            else:
                bvalue = b

            if avalue is None or bvalue is None:
                remained_SEGS.append(seg)
                continue

            avalue = float(avalue)
            bvalue = float(bvalue)

            if op == '>':
                cond = avalue > bvalue
            elif op == '<':
                cond = avalue < bvalue
            elif op == '>=':
                cond = avalue >= bvalue
            elif op == '<=':
                cond = avalue <= bvalue
            else:
                cond = avalue == bvalue

            if cond:
                filtered_SEGS.append(seg)
            else:
                remained_SEGS.append(seg)

        return ((segs[0], filtered_SEGS), (segs[0], remained_SEGS))

```
