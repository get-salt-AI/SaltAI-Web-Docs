---
tags:
- LLM
---

# ∞ Markdown Composer
## Documentation
- Class name: `LLMMarkdownComposer`
- Category: `SALT/Llama-Index/Tools/Markdown`
- Output node: `False`

The LLMMarkdownComposer node is designed to generate well-structured Markdown documents based on provided text input and classification criteria. It leverages a language model to interpret and transform the input text into Markdown format, ensuring the output adheres to Markdown standards while incorporating specified classifiers and any additional directions.
## Input types
### Required
- **`llm_model`**
    - Specifies the language model to use for generating the Markdown document. It's crucial for interpreting the input text and applying the classification criteria to produce the desired output.
    - Comfy dtype: `LLM_MODEL`
    - Python dtype: `dict`
- **`text_input`**
    - The primary text input from which the Markdown document will be generated. This text serves as the base content that will be structured and formatted according to Markdown standards.
    - Comfy dtype: `STRING`
    - Python dtype: `str`
- **`classifier_list`**
    - A comma-separated list of classifiers that guide the structuring and formatting of the Markdown document. These classifiers help tailor the output to specific requirements or themes.
    - Comfy dtype: `STRING`
    - Python dtype: `str`
### Optional
- **`extra_directions`**
    - Optional additional instructions for the language model to follow when generating the Markdown document. These can provide further context or specify particular formatting preferences.
    - Comfy dtype: `STRING`
    - Python dtype: `str`
## Output types
- **`markdown_output`**
    - Comfy dtype: `STRING`
    - The generated Markdown document, structured and formatted according to the input text, classifiers, and any additional directions provided.
    - Python dtype: `str`
## Usage tips
- Infra type: `GPU`
- Common nodes: unknown


## Source code
```python
class LLMMarkdownComposer:
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
    RETURN_NAMES = ("markdown_output",)

    FUNCTION = "compose_markdown"
    CATEGORY = "SALT/Llama-Index/Tools/Markdown"

    def compose_markdown(self, llm_model, text_input, classifier_list, extra_directions=""):
        classifier_list = [item.strip() for item in classifier_list.split(",") if item.strip()]
        prompt = (
            f"{text_input}\n\n###\n\n"
            "Given the above text, create a valid Markdown document utilizing *all* of the data; "
            f"using the following classifiers: {classifier_list}.\n\n"
            f"{extra_directions}\n\n"
            "Please ensure the Markdown output is well-structured, and does not omit any data."
        )
        
        response = llm_model['llm'].complete(prompt)
        
        return (response.text,)

```
