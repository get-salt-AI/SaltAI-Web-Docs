# ConditioningAverage
## Documentation
- Class name: `ConditioningAverage`
- Category: `conditioning`
- Output node: `False`

The `ConditioningAverage` node blends two sets of conditioning data by averaging them together, weighted by a specified strength. It handles both the tensor representations and optional pooled outputs, ensuring compatibility in dimensions and applying the weighted average accordingly.
## Input types
### Required
- **`conditioning_to`**
    - The target conditioning data to which the averaging operation will be applied. It plays a crucial role in determining the final output, as it is directly modified by the weighted average of itself and the `conditioning_from` data.
    - Python dtype: `List[Tuple[torch.Tensor, Dict[str, Any]]]`
    - Comfy dtype: `CONDITIONING`
- **`conditioning_from`**
    - The source conditioning data that will be averaged with the `conditioning_to` data. This input is essential for the blending process, contributing to the final conditioned output based on its weighted average.
    - Python dtype: `List[Tuple[torch.Tensor, Dict[str, Any]]]`
    - Comfy dtype: `CONDITIONING`
- **`conditioning_to_strength`**
    - A scalar value that determines the weight of the `conditioning_to` data in the averaging process. It directly influences the balance between the original and the blended conditioning data, affecting the final output.
    - Python dtype: `float`
    - Comfy dtype: `FLOAT`
## Output types
- **`conditioning`**
    - The result of the weighted averaging process, combining `conditioning_to` and `conditioning_from` data. It reflects the blended conditioning data, adjusted according to the specified strength.
    - Python dtype: `List[Tuple[torch.Tensor, Dict[str, Any]]]`
    - Comfy dtype: `CONDITIONING`
## Usage tips
- Infra type: `GPU`
- Common nodes: `FaceFixerOpenCV`


## Source code
```python
class ConditioningAverage :
    @classmethod
    def INPUT_TYPES(s):
        return {"required": {"conditioning_to": ("CONDITIONING", ), "conditioning_from": ("CONDITIONING", ),
                              "conditioning_to_strength": ("FLOAT", {"default": 1.0, "min": 0.0, "max": 1.0, "step": 0.01})
                             }}
    RETURN_TYPES = ("CONDITIONING",)
    FUNCTION = "addWeighted"

    CATEGORY = "conditioning"

    def addWeighted(self, conditioning_to, conditioning_from, conditioning_to_strength):
        out = []

        if len(conditioning_from) > 1:
            print("Warning: ConditioningAverage conditioning_from contains more than 1 cond, only the first one will actually be applied to conditioning_to.")

        cond_from = conditioning_from[0][0]
        pooled_output_from = conditioning_from[0][1].get("pooled_output", None)

        for i in range(len(conditioning_to)):
            t1 = conditioning_to[i][0]
            pooled_output_to = conditioning_to[i][1].get("pooled_output", pooled_output_from)
            t0 = cond_from[:,:t1.shape[1]]
            if t0.shape[1] < t1.shape[1]:
                t0 = torch.cat([t0] + [torch.zeros((1, (t1.shape[1] - t0.shape[1]), t1.shape[2]))], dim=1)

            tw = torch.mul(t1, conditioning_to_strength) + torch.mul(t0, (1.0 - conditioning_to_strength))
            t_to = conditioning_to[i][1].copy()
            if pooled_output_from is not None and pooled_output_to is not None:
                t_to["pooled_output"] = torch.mul(pooled_output_to, conditioning_to_strength) + torch.mul(pooled_output_from, (1.0 - conditioning_to_strength))
            elif pooled_output_from is not None:
                t_to["pooled_output"] = pooled_output_from

            n = [tw, t_to]
            out.append(n)
        return (out, )

```
