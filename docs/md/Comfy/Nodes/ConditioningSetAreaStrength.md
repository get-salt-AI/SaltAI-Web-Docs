# ConditioningSetAreaStrength
## Documentation
- Class name: `ConditioningSetAreaStrength`
- Category: `conditioning`
- Output node: `False`

The `ConditioningSetAreaStrength` node modifies the strength attribute of each conditioning element in a given list. This operation allows for the adjustment of the influence or intensity of the conditioning elements.
## Input types
### Required
- **`conditioning`**
    - The list of conditioning elements to be modified. Each element's strength attribute will be adjusted.
    - Python dtype: `List[Tuple[Any, Dict[str, Any]]]`
    - Comfy dtype: `CONDITIONING`
- **`strength`**
    - The new strength value to be applied to all conditioning elements. This value determines the intensity of the effect each conditioning element has.
    - Python dtype: `float`
    - Comfy dtype: `FLOAT`
## Output types
- **`conditioning`**
    - The modified list of conditioning elements, with updated strength values for each element.
    - Python dtype: `List[Tuple[Any, Dict[str, Any]]]`
    - Comfy dtype: `CONDITIONING`
## Usage tips
- Infra type: `CPU`
- Common nodes: unknown


## Source code
```python
class ConditioningSetAreaStrength:
    @classmethod
    def INPUT_TYPES(s):
        return {"required": {"conditioning": ("CONDITIONING", ),
                              "strength": ("FLOAT", {"default": 1.0, "min": 0.0, "max": 10.0, "step": 0.01}),
                             }}
    RETURN_TYPES = ("CONDITIONING",)
    FUNCTION = "append"

    CATEGORY = "conditioning"

    def append(self, conditioning, strength):
        c = []
        for t in conditioning:
            n = [t[0], t[1].copy()]
            n[1]['strength'] = strength
            c.append(n)
        return (c, )

```
