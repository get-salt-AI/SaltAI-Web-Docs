---
tags:
- Text
---

# ∞ Regex Creator
## Documentation
- Class name: `LLMRegexCreator`
- Category: `SALT/Llama-Index/Tools/Regex`
- Output node: `False`

The LLMRegexCreator node is designed to generate regular expression (regex) patterns based on a given description and any additional directions. It utilizes a language model to interpret the requirements and produce a well-formed regex pattern that accurately matches the specified criteria.
## Input types
### Required
- **`llm_model`**
    - Specifies the language model to use for generating the regex pattern. It is crucial for interpreting the description and crafting the regex.
    - Comfy dtype: `LLM_MODEL`
    - Python dtype: `dict`
- **`description`**
    - The description of the regex pattern to be created, which may optionally include examples. This is the primary input based on which the regex is generated.
    - Comfy dtype: `STRING`
    - Python dtype: `str`
### Optional
- **`extra_directions`**
    - Additional instructions or constraints to guide the regex generation process, enhancing the specificity and accuracy of the output.
    - Comfy dtype: `STRING`
    - Python dtype: `str`
## Output types
- **`regex_pattern`**
    - Comfy dtype: `STRING`
    - The generated regex pattern, which is a concise and accurate representation of the described criteria.
    - Python dtype: `str`
## Usage tips
- Infra type: `CPU`
- Common nodes: unknown


## Source code
```python
class LLMRegexCreator:
    @classmethod
    def INPUT_TYPES(cls):
        return {
            "required": {
                "llm_model": ("LLM_MODEL",),
                "description": ("STRING", {"multiline": True, "dynamicPrompts": False, "placeholder": "Describe regex pattern to create, optionally provide example"}),
            },
            "optional": {
                "extra_directions": ("STRING", {"multiline": True, "dynamicPrompts": False, "placeholder": "Extra directions for the LLM to follow..."}),
            }
        }

    RETURN_TYPES = ("STRING",)
    RETURN_NAMES = ("regex_pattern",)

    FUNCTION = "create_regex"
    CATEGORY = "SALT/Llama-Index/Tools/Regex"

    def create_regex(self, llm_model, description, extra_directions=""):
        prompt = (
            f"Create only a well formed regex pattern based on the following description:\n\n"
            f"{description}\n\n"
            f"{extra_directions}\n\n"
            "Please ensure the regex pattern is concise and accurately matches the described criteria."
        )
        
        response = llm_model['llm'].complete(prompt)
        
        return (response.text,)

```
