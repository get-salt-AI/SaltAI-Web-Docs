---
tags:
- LLM
---

# ∞ HTML Composer
## Documentation
- Class name: `LLMHtmlComposer`
- Category: `SALT/Llama-Index/Tools/HTML`
- Output node: `False`

The LLMHtmlComposer node is designed to generate HTML content based on given text input, utilizing specified classifiers and any additional directions. It aims to produce well-structured, valid HTML documents or snippets, depending on the mode selected, ensuring all provided data is included in the output.
## Input types
### Required
- **`llm_model`**
    - The language model used for generating HTML content. It plays a crucial role in interpreting the input text and classifiers to produce the desired HTML output.
    - Comfy dtype: `LLM_MODEL`
    - Python dtype: `Dict[str, Any]`
- **`text_input`**
    - The primary text input from which the HTML content will be generated. This text serves as the basis for the content creation process.
    - Comfy dtype: `STRING`
    - Python dtype: `str`
- **`classifier_list`**
    - A list of classifiers used to guide the generation of HTML content, ensuring the output adheres to specified criteria.
    - Comfy dtype: `STRING`
    - Python dtype: `List[str]`
### Optional
- **`extra_directions`**
    - Additional instructions provided to the language model to influence the generation of HTML content, allowing for more customized outputs.
    - Comfy dtype: `STRING`
    - Python dtype: `str`
- **`composer_mode`**
    - Determines whether the output will be a full HTML page document or an HTML snippet, affecting the structure and scope of the generated content.
    - Comfy dtype: `COMBO[STRING]`
    - Python dtype: `str`
## Output types
- **`html_output`**
    - Comfy dtype: `STRING`
    - The generated HTML content, either as a full document or a snippet, based on the input text, classifiers, and any additional directions.
    - Python dtype: `str`
## Usage tips
- Infra type: `GPU`
- Common nodes: unknown


## Source code
```python
class LLMHtmlComposer:
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
                "composer_mode": (["full_markup", "blocked_markup"],)
            }
        }

    RETURN_TYPES = ("STRING",)
    RETURN_NAMES = ("html_output",)

    FUNCTION = "compose_html"
    CATEGORY = "SALT/Llama-Index/Tools/HTML"

    def compose_html(self, llm_model, text_input, classifier_list, extra_directions="", composer_mode="full_markup"):
        classifier_list = [item.strip() for item in classifier_list.split(",") if item.strip()]
        markup_style = "full HTML page document" if composer_mode == "full_markup" else "HTML snippet (without html, head, body or any extraneous containers)"
        prompt = (
            f"{text_input}\n\n###\n\n"
            f"Given the above text, create a valid {markup_style} utilizing *all* of the data, intact; "
            f"using the following classifiers: {classifier_list}.\n\n"
            f"{extra_directions}\n\n"
            "Please ensure the HTML output is well-structured, valid, and does not omit any data."
        )
        
        response = llm_model['llm'].complete(prompt)
        
        return (response.text,)

```
