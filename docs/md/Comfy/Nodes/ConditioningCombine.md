# Conditioning (Combine)
## Documentation
- Class name: `ConditioningCombine`
- Category: `conditioning`
- Output node: `False`

This node combines two conditioning inputs into a single output by simply adding them together. It's useful for merging conditioning information from different sources.
## Input types
### Required
- **`conditioning_1`**
    - The first conditioning input to be combined. It plays an equal role in the combination process as `conditioning_2`.
    - Python dtype: `Tuple[torch.Tensor, Dict[str, Any]]`
    - Comfy dtype: `CONDITIONING`
- **`conditioning_2`**
    - The second conditioning input to be combined. It is equally important in the combination process as `conditioning_1`.
    - Python dtype: `Tuple[torch.Tensor, Dict[str, Any]]`
    - Comfy dtype: `CONDITIONING`
## Output types
- **`conditioning`**
    - The result of combining `conditioning_1` and `conditioning_2`, providing a merged conditioning output.
    - Python dtype: `Tuple[torch.Tensor, Dict[str, Any]]`
    - Comfy dtype: `CONDITIONING`
## Usage tips
- Infra type: `CPU`
- Common nodes: `KSampler,ConditioningCombine,KSampler Adv. (Efficient),CR Conditioning Input Switch,Attention couple,KSampler with Variations,CR Module Pipe Loader,ControlNetApplyAdvanced`


## Source code
```python
class ConditioningCombine:
    @classmethod
    def INPUT_TYPES(s):
        return {"required": {"conditioning_1": ("CONDITIONING", ), "conditioning_2": ("CONDITIONING", )}}
    RETURN_TYPES = ("CONDITIONING",)
    FUNCTION = "combine"

    CATEGORY = "conditioning"

    def combine(self, conditioning_1, conditioning_2):
        return (conditioning_1 + conditioning_2, )

```
