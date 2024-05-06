---
tags:
- LLM
---

# ∞ JSON Composer
## Documentation
- Class name: `LLMJsonComposer`
- Category: `SALT/Llama-Index/Tools/JSON`
- Output node: `False`

The LLMJsonComposer node is designed to generate JSON objects from given text inputs, utilizing specified classifiers and any additional directions provided. It abstracts the complexity of JSON composition, ensuring the output is well-formatted and comprehensive.
## Input types
### Required
- **`llm_model`**
    - Specifies the language model to use for generating the JSON object, central to the node's operation.
    - Comfy dtype: `LLM_MODEL`
    - Python dtype: `dict`
- **`text_input`**
    - The primary text input from which the JSON object will be generated, serving as the base content for the composition.
    - Comfy dtype: `STRING`
    - Python dtype: `str`
- **`classifier_list`**
    - A list of classifiers to guide the JSON composition process, enhancing the relevance and accuracy of the generated JSON.
    - Comfy dtype: `STRING`
    - Python dtype: `str`
### Optional
- **`extra_directions`**
    - Optional additional instructions to further guide the JSON composition, allowing for more tailored outputs.
    - Comfy dtype: `STRING`
    - Python dtype: `str`
## Output types
- **`json_output`**
    - Comfy dtype: `STRING`
    - The generated JSON object, formatted according to the input specifications and additional directions.
    - Python dtype: `str`
## Usage tips
- Infra type: `CPU`
- Common nodes: unknown


## Source code
```python
class LLMJsonComposer:
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
    RETURN_NAMES = ("json_output",)

    FUNCTION = "compose_json"
    CATEGORY = "SALT/Llama-Index/Tools/JSON"

    def compose_json(self, llm_model, text_input, classifier_list, extra_directions=""):
        classifier_list = [item.strip() for item in classifier_list.split(",") if item.strip()]
        prompt = f"{text_input}\n\n###\n\nGiven the above text, create a valid JSON object utilizing *all* of the data; using the following classifiers: {classifier_list}.\n\n{extra_directions}\n\nPlease ensure the JSON output is properly formatted, and does not omit any data."
        response = llm_model['llm'].complete(prompt)
        return (response.text,)

```
