---
tags:
- LLM
---

# ∞ JSON Repair
## Documentation
- Class name: `LLMJsonRepair`
- Category: `SALT/Llama-Index/Tools/JSON`
- Output node: `False`

The LLMJsonRepair node is designed to correct malformed JSON strings using a language model, ensuring the output is valid JSON that retains the original data without any loss.
## Input types
### Required
- **`llm_model`**
    - Specifies the language model to use for repairing the JSON, acting as the core processing unit for interpreting and correcting the malformed input.
    - Comfy dtype: `LLM_MODEL`
    - Python dtype: `Dict[str, Any]`
- **`text_input`**
    - The malformed JSON string that needs to be repaired, serving as the primary input for the correction process.
    - Comfy dtype: `STRING`
    - Python dtype: `str`
### Optional
- **`extra_directions`**
    - Optional additional instructions to guide the language model in the repair process, allowing for more tailored corrections.
    - Comfy dtype: `STRING`
    - Python dtype: `str`
## Output types
- **`json_output`**
    - Comfy dtype: `STRING`
    - The repaired JSON string, corrected to be well-formed and valid while preserving the original data.
    - Python dtype: `str`
## Usage tips
- Infra type: `CPU`
- Common nodes: unknown


## Source code
```python
class LLMJsonRepair:
    @classmethod
    def INPUT_TYPES(cls):
        return {
            "required": {
                "llm_model": ("LLM_MODEL",),
                "text_input": ("STRING", {"multiline": True, "dynamicPrompts": False, "placeholder": "Malformed JSON..."}),
            },
            "optional": {
                "extra_directions": ("STRING", {"multiline": True, "dynamicPrompts": False, "placeholder": "Extra directions for the LLM to follow..."}),
            }
        }

    RETURN_TYPES = ("STRING",)
    RETURN_NAMES = ("json_output",)

    FUNCTION = "compose_json"
    CATEGORY = "SALT/Llama-Index/Tools/JSON"

    def compose_json(self, llm_model, text_input, extra_directions=""):
        prompt = (
            f"{text_input}\n\n###\n\n"
            "Given the above malformed JSON, please inspect it and repair it so that it's valid JSON, without changing or loosing any data if possible."
            f"{extra_directions}\n\n"
            "Please ensure the JSON output is properly formatted, and does not omit any data."
        )
        
        response = llm_model['llm'].complete(prompt)
        
        return (response.text,)

```
