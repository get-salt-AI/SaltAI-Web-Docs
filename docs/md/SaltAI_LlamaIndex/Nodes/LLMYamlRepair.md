---
tags:
- LLM
---

# ∞ YAML Repair
## Documentation
- Class name: `LLMYamlRepair`
- Category: `SALT/Llama-Index/Tools/YAML`
- Output node: `False`

The LLMYamlRepair node is designed to correct malformed YAML documents. It leverages a language model to analyze and repair the YAML, ensuring the output is valid and retains all original data without alteration.
## Input types
### Required
- **`llm_model`**
    - Specifies the language model to use for repairing the YAML document. It's crucial for interpreting the input and generating the corrected output.
    - Comfy dtype: `LLM_MODEL`
    - Python dtype: `Dict[str, Any]`
- **`text_input`**
    - The malformed YAML content that needs to be repaired. This is the primary input from which the node will generate a valid YAML document.
    - Comfy dtype: `STRING`
    - Python dtype: `str`
### Optional
- **`extra_directions`**
    - Additional instructions or guidelines for the language model to follow while repairing the YAML document. This allows for customizing the repair process.
    - Comfy dtype: `STRING`
    - Python dtype: `str`
## Output types
- **`yaml_output`**
    - Comfy dtype: `STRING`
    - The repaired YAML document, corrected to be valid YAML while preserving the original data.
    - Python dtype: `str`
## Usage tips
- Infra type: `CPU`
- Common nodes: unknown


## Source code
```python
class LLMYamlRepair:
    @classmethod
    def INPUT_TYPES(cls):
        return {
            "required": {
                "llm_model": ("LLM_MODEL",),
                "text_input": ("STRING", {"multiline": True, "dynamicPrompts": False, "placeholder": "Malformed YAML..."}),
            },
            "optional": {
                "extra_directions": ("STRING", {"multiline": True, "dynamicPrompts": False, "placeholder": "Extra directions for the LLM to follow..."}),
            }
        }

    RETURN_TYPES = ("STRING",)
    RETURN_NAMES = ("yaml_output",)

    FUNCTION = "repair_yaml"
    CATEGORY = "SALT/Llama-Index/Tools/YAML"

    def repair_yaml(self, llm_model, text_input, extra_directions=""):
        prompt = (
            f"{text_input}\n\n###\n\n"
            "Given the above malformed YAML, please inspect it and repair it so that it's valid YAML, without changing or losing any data if possible."
            f"{extra_directions}\n\n"
            "Please ensure the YAML output is properly formatted, and does not omit any data."
        )
        
        response = llm_model['llm'].complete(prompt)
        
        return (response.text,)

```
