# ImpactWildcardProcessor
## Documentation
- Class name: `ImpactWildcardProcessor`
- Category: `ImpactPack/Prompt`
- Output node: `False`

The `ImpactWildcardProcessor` node is designed to process text inputs by populating them with dynamic content based on wildcards. It allows for the text to be either populated with dynamic values or kept fixed, based on the mode selected. This functionality is crucial for generating dynamic content that can adapt to different contexts or requirements.
## Input types
### Required
- **`wildcard_text`**
    - The text containing wildcards that need to be populated or processed. This parameter is essential for defining the template of the content that will be dynamically generated.
    - Python dtype: `str`
    - Comfy dtype: `STRING`
- **`populated_text`**
    - The text after wildcards have been populated. This parameter is crucial for receiving the output of the dynamic content generation process.
    - Python dtype: `str`
    - Comfy dtype: `STRING`
- **`mode`**
    - Determines whether the text should be populated with dynamic values (Populate) or kept as is (Fixed). This affects how the node processes the input text.
    - Python dtype: `bool`
    - Comfy dtype: `BOOLEAN`
- **`seed`**
    - A seed value for random number generation, affecting the selection of values for wildcard population. This ensures reproducibility of the dynamic content generation.
    - Python dtype: `int`
    - Comfy dtype: `INT`
- **`Select to add Wildcard`**
    - Allows selection of a specific wildcard to add to the text, enhancing the customization of the dynamic content generation.
    - Python dtype: `List[str]`
    - Comfy dtype: `STRING`
## Output types
- **`string`**
    - The final text after all wildcards have been processed and populated, ready for use.
    - Python dtype: `str`
    - Comfy dtype: `STRING`
## Usage tips
- Infra type: `CPU`
- Common nodes: `CLIPTextEncode`


## Source code
```python
class ImpactWildcardProcessor:
    @classmethod
    def INPUT_TYPES(s):
        return {"required": {
                        "wildcard_text": ("STRING", {"multiline": True, "dynamicPrompts": False}),
                        "populated_text": ("STRING", {"multiline": True, "dynamicPrompts": False}),
                        "mode": ("BOOLEAN", {"default": True, "label_on": "Populate", "label_off": "Fixed"}),
                        "seed": ("INT", {"default": 0, "min": 0, "max": 0xffffffffffffffff}),
                        "Select to add Wildcard": (["Select the Wildcard to add to the text"],),
                    },
                }

    CATEGORY = "ImpactPack/Prompt"

    RETURN_TYPES = ("STRING", )
    FUNCTION = "doit"

    @staticmethod
    def process(**kwargs):
        return impact.wildcards.process(**kwargs)

    def doit(self, *args, **kwargs):
        populated_text = kwargs['populated_text']
        return (populated_text, )

```
