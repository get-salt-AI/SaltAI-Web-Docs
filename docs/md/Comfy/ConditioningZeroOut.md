# ConditioningZeroOut
## Documentation
- Class name: `ConditioningZeroOut`
- Category: `advanced/conditioning`
- Output node: `False`

The `ConditioningZeroOut` node zeroes out the `pooled_output` tensor within each conditioning element, effectively neutralizing its influence in subsequent operations. This operation is useful for selectively disabling parts of the conditioning data.
## Input types
### Required
- **`conditioning`**
    - The conditioning data to be modified. This operation zeroes out the `pooled_output` tensor within each element, which can be useful for experiments or when certain parts of the conditioning are not desired.
    - Python dtype: `List[Tuple[torch.Tensor, Dict[str, torch.Tensor]]]`
    - Comfy dtype: `CONDITIONING`
## Output types
- **`conditioning`**
    - The modified conditioning data with the `pooled_output` tensor zeroed out in each element. This allows for selective disabling of parts of the conditioning.
    - Python dtype: `List[Tuple[torch.Tensor, Dict[str, torch.Tensor]]]`
    - Comfy dtype: `CONDITIONING`
## Usage tips
- Infra type: `GPU`
- Common nodes: `UltimateSDUpscale`


## Source code
```python
class ConditioningZeroOut:
    @classmethod
    def INPUT_TYPES(s):
        return {"required": {"conditioning": ("CONDITIONING", )}}
    RETURN_TYPES = ("CONDITIONING",)
    FUNCTION = "zero_out"

    CATEGORY = "advanced/conditioning"

    def zero_out(self, conditioning):
        c = []
        for t in conditioning:
            d = t[1].copy()
            if "pooled_output" in d:
                d["pooled_output"] = torch.zeros_like(d["pooled_output"])
            n = [torch.zeros_like(t[0]), d]
            c.append(n)
        return (c, )

```
