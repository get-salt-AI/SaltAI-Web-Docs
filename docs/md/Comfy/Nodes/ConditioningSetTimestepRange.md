# ConditioningSetTimestepRange
## Documentation
- Class name: `ConditioningSetTimestepRange`
- Category: `advanced/conditioning`
- Output node: `False`

This node is designed to adjust the temporal aspect of conditioning by setting a specific range of timesteps. It allows for the precise control over the start and end points of the conditioning process, enabling more targeted and efficient generation.
## Input types
### Required
- **`conditioning`**
    - Comfy dtype: `CONDITIONING`
    - The conditioning input represents the current state of the generation process, which this node modifies by setting a specific range of timesteps.
    - Python dtype: `List[Tuple[torch.Tensor, Dict[str, Any]]]`
- **`start`**
    - Comfy dtype: `FLOAT`
    - The start parameter specifies the beginning of the timestep range as a percentage of the total generation process, allowing for fine-tuned control over when the conditioning effects begin.
    - Python dtype: `float`
- **`end`**
    - Comfy dtype: `FLOAT`
    - The end parameter defines the endpoint of the timestep range as a percentage, enabling precise control over the duration and conclusion of the conditioning effects.
    - Python dtype: `float`
## Output types
- **`conditioning`**
    - Comfy dtype: `CONDITIONING`
    - The output is the modified conditioning with the specified timestep range applied, ready for further processing or generation.
    - Python dtype: `List[Tuple[torch.Tensor, Dict[str, Any]]]`
## Usage tips
- Infra type: `CPU`
- Common nodes: `CR Conditioning Mixer`


## Source code
```python
class ConditioningSetTimestepRange:
    @classmethod
    def INPUT_TYPES(s):
        return {"required": {"conditioning": ("CONDITIONING", ),
                             "start": ("FLOAT", {"default": 0.0, "min": 0.0, "max": 1.0, "step": 0.001}),
                             "end": ("FLOAT", {"default": 1.0, "min": 0.0, "max": 1.0, "step": 0.001})
                             }}
    RETURN_TYPES = ("CONDITIONING",)
    FUNCTION = "set_range"

    CATEGORY = "advanced/conditioning"

    def set_range(self, conditioning, start, end):
        c = []
        for t in conditioning:
            d = t[1].copy()
            d['start_percent'] = start
            d['end_percent'] = end
            n = [t[0], d]
            c.append(n)
        return (c, )

```
