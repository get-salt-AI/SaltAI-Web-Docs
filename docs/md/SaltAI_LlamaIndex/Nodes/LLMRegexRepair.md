---
tags:
- Text
---

# ∞ Regex Repair
## Documentation
- Class name: `LLMRegexRepair`
- Category: `SALT/Llama-Index/Tools/Regex`
- Output node: `False`

The LLMRegexRepair node is designed to correct potentially malformed or incorrect regular expression (regex) patterns. It leverages a language model to interpret the user's description of what the regex should match and the issues with the current pattern, then generates a repaired version that aligns with the specified criteria.
## Input types
### Required
- **`llm_model`**
    - Specifies the language model to use for repairing the regex pattern. It is crucial for interpreting the input pattern and description to generate a corrected version.
    - Comfy dtype: `LLM_MODEL`
    - Python dtype: `dict`
- **`text_input`**
    - The potentially malformed or incorrect regex pattern that needs to be repaired. It serves as the primary input for the repair process.
    - Comfy dtype: `STRING`
    - Python dtype: `str`
- **`description`**
    - A description of what the regex pattern is intended to match and the issues with the current pattern. This context helps guide the repair process.
    - Comfy dtype: `STRING`
    - Python dtype: `str`
### Optional
- **`extra_directions`**
    - Optional additional instructions or constraints to guide the repair process, allowing for more tailored corrections.
    - Comfy dtype: `STRING`
    - Python dtype: `str`
## Output types
- **`repaired_regex_pattern`**
    - Comfy dtype: `STRING`
    - The corrected regex pattern, generated based on the input pattern and description provided.
    - Python dtype: `str`
## Usage tips
- Infra type: `CPU`
- Common nodes: unknown


## Source code
```python
class LLMRegexRepair:
    @classmethod
    def INPUT_TYPES(cls):
        return {
            "required": {
                "llm_model": ("LLM_MODEL",),
                "text_input": ("STRING", {"multiline": True, "dynamicPrompts": False, "placeholder": "Enter the malformed regex pattern here"}),
                "description": ("STRING", {"multiline": True, "dynamicPrompts": False, "placeholder": "Describe what the regex pattern does wrong, and what it should do."}),
            },
            "optional": {
                "extra_directions": ("STRING", {"multiline": True, "dynamicPrompts": False, "placeholder": "Extra directions for the LLM to follow, such as specific constraints or formats"}),
            }
        }

    RETURN_TYPES = ("STRING",)
    RETURN_NAMES = ("repaired_regex_pattern",)

    FUNCTION = "repair_regex"
    CATEGORY = "SALT/Llama-Index/Tools/Regex"

    def repair_regex(self, llm_model, text_input, description, extra_directions=""):
        prompt = (
            f"Given the potentially malformed or incorrect regex pattern:\n\n{text_input}\n\n"
            f"and the following description of what the pattern should match:\n\n{description}\n\n"
            f"{extra_directions}\n\n"
            "Please repair the regex pattern so it is well-formed and accurately matches the described criteria."
        )
        
        response = llm_model['llm'].complete(prompt)
        
        return (response.text,)

```
