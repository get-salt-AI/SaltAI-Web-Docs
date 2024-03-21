# ConditioningSetTimestepRange
## Documentation
- Class name: `ConditioningSetTimestepRange`
- Category: `advanced/conditioning`
- Output node: `False`

This node modifies the conditioning data by setting a specific range of timesteps for the conditioning process. It allows for the precise control of the start and end points of the conditioning, enabling more targeted and efficient generation.
## Input types
### Required
- **`conditioning`**
    - The conditioning data to be modified. It serves as the basis for the generation process, dictating the characteristics and constraints of the output.
    - Python dtype: `List[Tuple[torch.Tensor, Dict[str, Any]]]`
    - Comfy dtype: `CONDITIONING`
- **`start`**
    - The starting point of the timestep range, as a percentage of the total timesteps. It specifies the beginning of the conditioning effect.
    - Python dtype: `float`
    - Comfy dtype: `FLOAT`
- **`end`**
    - The ending point of the timestep range, as a percentage of the total timesteps. It determines when the conditioning effect concludes.
    - Python dtype: `float`
    - Comfy dtype: `FLOAT`
## Output types
- **`conditioning`**
    - The modified conditioning data with the specified timestep range applied. This data is used for further processing or generation.
    - Python dtype: `List[Tuple[torch.Tensor, Dict[str, Any]]]`
    - Comfy dtype: `CONDITIONING`
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
