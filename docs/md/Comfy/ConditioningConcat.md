# Conditioning (Concat)
## Documentation
- Class name: `ConditioningConcat`
- Category: `conditioning`
- Output node: `False`

The `ConditioningConcat` node concatenates the conditioning information from one source to another. It specifically takes the first conditioning element from the `conditioning_from` input and concatenates it to each element in the `conditioning_to` input, effectively applying the conditioning from one source across another.
## Input types
### Required
- **`conditioning_to`**
    - The target conditioning data to which the conditioning from another source will be concatenated. It plays a crucial role in determining the final output by receiving additional conditioning information.
    - Python dtype: `List[Tuple[torch.Tensor, Dict[str, Any]]]`
    - Comfy dtype: `CONDITIONING`
- **`conditioning_from`**
    - The source of conditioning data to be concatenated to the target conditioning data. Only the first element of this input is used, which emphasizes its selective influence on the target conditioning.
    - Python dtype: `List[Tuple[torch.Tensor, Dict[str, Any]]]`
    - Comfy dtype: `CONDITIONING`
## Output types
- **`conditioning`**
    - The output is the modified target conditioning data with the first element from the source conditioning data concatenated to each of its elements.
    - Python dtype: `Tuple[List[Tuple[torch.Tensor, Dict[str, Any]]]]`
    - Comfy dtype: `CONDITIONING`
## Usage tips
- Infra type: `GPU`
- Common nodes: `ControlNetApplyAdvanced,SamplerCustom,ConditioningConcat`


## Source code
```python
class ConditioningConcat:
    @classmethod
    def INPUT_TYPES(s):
        return {"required": {
            "conditioning_to": ("CONDITIONING",),
            "conditioning_from": ("CONDITIONING",),
            }}
    RETURN_TYPES = ("CONDITIONING",)
    FUNCTION = "concat"

    CATEGORY = "conditioning"

    def concat(self, conditioning_to, conditioning_from):
        out = []

        if len(conditioning_from) > 1:
            print("Warning: ConditioningConcat conditioning_from contains more than 1 cond, only the first one will actually be applied to conditioning_to.")

        cond_from = conditioning_from[0][0]

        for i in range(len(conditioning_to)):
            t1 = conditioning_to[i][0]
            tw = torch.cat((t1, cond_from),1)
            n = [tw, conditioning_to[i][1].copy()]
            out.append(n)

        return (out, )

```
