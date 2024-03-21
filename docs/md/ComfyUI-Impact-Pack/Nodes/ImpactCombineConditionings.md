# Combine Conditionings
## Documentation
- Class name: `ImpactCombineConditionings`
- Category: `ImpactPack/Util`
- Output node: `False`

The `ImpactCombineConditionings` node is designed to merge multiple conditioning inputs into a single conditioning output. This process involves concatenating the conditioning data from all provided inputs, potentially modifying the structure or content of the conditioning to accommodate the combination. This node is useful in scenarios where multiple conditioning sources need to be unified for further processing or application.
## Input types
### Required
- **`conditioning1`**
    - The primary conditioning input to be combined. It serves as the initial base to which additional conditioning inputs are added, playing a crucial role in the combination process.
    - Python dtype: `List[Tuple[torch.Tensor, Any]]`
    - Comfy dtype: `CONDITIONING`
## Output types
- **`conditioning`**
    - The result of combining multiple conditioning inputs. This output represents a unified conditioning that incorporates elements from all provided inputs.
    - Python dtype: `List[Tuple[torch.Tensor, Any]]`
    - Comfy dtype: `CONDITIONING`
## Usage tips
- Infra type: `GPU`
- Common nodes: `SetNode`


## Source code
```python
class CombineConditionings:
    @classmethod
    def INPUT_TYPES(s):
        return {"required": {
                     "conditioning1": ("CONDITIONING", ),
                     },
                }

    RETURN_TYPES = ("CONDITIONING", )
    FUNCTION = "doit"

    CATEGORY = "ImpactPack/Util"

    def doit(self, **kwargs):
        res = []
        for k, v in kwargs.items():
            res += v

        return (res, )

```
