---
tags:
- LLM
---

# ∞ YAML Composer
## Documentation
- Class name: `LLMYamlComposer`
- Category: `SALT/Llama-Index/Tools/YAML`
- Output node: `False`

The LLMYamlComposer node is designed to generate YAML documents based on provided text input, classifier list, and optional extra directions. It leverages a language model to compose valid YAML structures that incorporate all specified data, ensuring the output is properly formatted and comprehensive.
## Input types
### Required
- **`llm_model`**
    - Specifies the language model to use for generating the YAML document. It's crucial for interpreting the input text and classifiers to produce the desired output.
    - Comfy dtype: `LLM_MODEL`
    - Python dtype: `Dict[str, Any]`
- **`text_input`**
    - The primary text input from which the YAML document will be generated. This text serves as the basis for the content of the YAML output.
    - Comfy dtype: `STRING`
    - Python dtype: `str`
- **`classifier_list`**
    - A comma-separated list of classifiers to guide the YAML generation process, ensuring the output aligns with specified categories or types.
    - Comfy dtype: `STRING`
    - Python dtype: `str`
### Optional
- **`extra_directions`**
    - Optional additional instructions to further guide the YAML generation, allowing for more tailored outputs.
    - Comfy dtype: `STRING`
    - Python dtype: `str`
## Output types
- **`yaml_output`**
    - Comfy dtype: `STRING`
    - The generated YAML document, properly formatted and incorporating all specified data and classifiers.
    - Python dtype: `str`
## Usage tips
- Infra type: `CPU`
- Common nodes: unknown


## Source code
```python
class LLMYamlComposer:
    @classmethod
    def INPUT_TYPES(cls):
        return {
            "required": {
                "llm_model": ("LLM_MODEL",),
                "text_input": ("STRING", {"multiline": True, "dynamicPrompts": False, "placeholder": "Data..."}),
                "classifier_list": ("STRING", {"multiline": False, "dynamicPrompts": False}),
            },
            "optional": {
                "extra_directions": ("STRING", {"multiline": True, "dynamicPrompts": False, "placeholder": "Extra directions for the LLM to follow..."}),
            }
        }

    RETURN_TYPES = ("STRING",)
    RETURN_NAMES = ("yaml_output",)

    FUNCTION = "compose_yaml"
    CATEGORY = "SALT/Llama-Index/Tools/YAML"

    def compose_yaml(self, llm_model, text_input, classifier_list, extra_directions=""):
        classifier_list = [item.strip() for item in classifier_list.split(",") if item.strip()]
        prompt = (
            f"{text_input}\n\n###\n\n"
            "Given the above text, create a valid YAML document utilizing *all* of the data; "
            f"using the following classifiers: {classifier_list}.\n\n"
            f"{extra_directions}\n\n"
            "Please ensure the YAML output is properly formatted, and does not omit any data."
        )
        
        response = llm_model['llm'].complete(prompt)
        
        return (response.text,)

```
