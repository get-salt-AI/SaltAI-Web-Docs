# RegionalPrompt
## Documentation
- Class name: `RegionalPrompt`
- Category: `ImpactPack/Regional`
- Output node: `False`

This node generates regional prompts based on a given mask and an advanced sampler. It utilizes the core functionality to create a regional prompt that can be used for further processing or generation tasks.
## Input types
### Required
- **`mask`**
    - The mask parameter is crucial for defining the region of interest within an image or a dataset. It directly influences the generation of regional prompts by specifying the area to focus on.
    - Python dtype: `torch.Tensor`
    - Comfy dtype: `MASK`
- **`advanced_sampler`**
    - The advanced_sampler parameter is essential for determining how the regional prompts are generated. It affects the complexity and quality of the generated prompts, making it a key component in the generation process.
    - Python dtype: `torch.nn.Module`
    - Comfy dtype: `KSAMPLER_ADVANCED`
## Output types
- **`regional_prompts`**
    - The output is a list of regional prompts generated based on the specified mask and advanced sampler. These prompts can be used for targeted generation or modification tasks.
    - Python dtype: `List[torch.Tensor]`
    - Comfy dtype: `REGIONAL_PROMPTS`
## Usage tips
- Infra type: `GPU`
- Common nodes: unknown


## Source code
```python
class RegionalPrompt:
    @classmethod
    def INPUT_TYPES(s):
        return {"required": {
                     "mask": ("MASK", ),
                     "advanced_sampler": ("KSAMPLER_ADVANCED", ),
                     },
                }

    RETURN_TYPES = ("REGIONAL_PROMPTS", )
    FUNCTION = "doit"

    CATEGORY = "ImpactPack/Regional"

    def doit(self, mask, advanced_sampler):
        regional_prompt = core.REGIONAL_PROMPT(mask, advanced_sampler)
        return ([regional_prompt], )

```
