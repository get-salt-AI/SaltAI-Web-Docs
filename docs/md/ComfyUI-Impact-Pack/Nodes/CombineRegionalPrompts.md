# CombineRegionalPrompts
## Documentation
- Class name: `CombineRegionalPrompts`
- Category: `ImpactPack/Regional`
- Output node: `False`

The `CombineRegionalPrompts` node is designed to aggregate multiple regional prompts into a single collection. This is particularly useful in scenarios where prompts from different regions need to be combined for further processing or analysis.
## Input types
### Required
- **`regional_prompts1`**
    - This parameter accepts a collection of regional prompts. It plays a crucial role in the node's functionality by allowing the aggregation of various regional prompts into a single, unified collection.
    - Python dtype: `List[REGIONAL_PROMPTS]`
    - Comfy dtype: `REGIONAL_PROMPTS`
## Output types
- **`regional_prompts`**
    - This output is a collection of regional prompts that have been aggregated from the input. It serves as a unified set of prompts for further operations or analyses.
    - Python dtype: `List[REGIONAL_PROMPTS]`
    - Comfy dtype: `REGIONAL_PROMPTS`
## Usage tips
- Infra type: `CPU`
- Common nodes: unknown


## Source code
```python
class CombineRegionalPrompts:
    @classmethod
    def INPUT_TYPES(s):
        return {"required": {
                     "regional_prompts1": ("REGIONAL_PROMPTS", ),
                     },
                }

    RETURN_TYPES = ("REGIONAL_PROMPTS", )
    FUNCTION = "doit"

    CATEGORY = "ImpactPack/Regional"

    def doit(self, **kwargs):
        res = []
        for k, v in kwargs.items():
            res += v

        return (res, )

```
