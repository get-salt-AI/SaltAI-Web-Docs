# Concat Conditionings
## Documentation
- Class name: `ImpactConcatConditionings`
- Category: `ImpactPack/__for_testing`
- Output node: `False`

This node concatenates multiple conditioning inputs into a single conditioning output. It iterates through each conditioning input, concatenates the first conditioning of each input with the conditioning to be concatenated to, and updates the output conditioning accordingly.
## Input types
### Required
- **`conditioning1`**
    - The primary conditioning to which other conditionings will be concatenated. It serves as the base for the concatenation process.
    - Python dtype: `List[Tuple[torch.Tensor, Any]]`
    - Comfy dtype: `CONDITIONING`
## Output types
- **`conditioning`**
    - The result of concatenating the specified conditionings. It represents the combined information from all inputs.
    - Python dtype: `List[Tuple[torch.Tensor, Any]]`
    - Comfy dtype: `CONDITIONING`
## Usage tips
- Infra type: `GPU`
- Common nodes: unknown


## Source code
```python
class ConcatConditionings:
    @classmethod
    def INPUT_TYPES(s):
        return {"required": {
                     "conditioning1": ("CONDITIONING", ),
                     },
                }

    RETURN_TYPES = ("CONDITIONING", )
    FUNCTION = "doit"

    CATEGORY = "ImpactPack/__for_testing"

    def doit(self, **kwargs):
        conditioning_to = list(kwargs.values())[0]

        for k, conditioning_from in list(kwargs.items())[1:]:
            out = []
            if len(conditioning_from) > 1:
                print("Warning: ConcatConditionings {k} contains more than 1 cond, only the first one will actually be applied to conditioning1.")

            cond_from = conditioning_from[0][0]

            for i in range(len(conditioning_to)):
                t1 = conditioning_to[i][0]
                tw = torch.cat((t1, cond_from), 1)
                n = [tw, conditioning_to[i][1].copy()]
                out.append(n)

            conditioning_to = out

        return (out, )

```
