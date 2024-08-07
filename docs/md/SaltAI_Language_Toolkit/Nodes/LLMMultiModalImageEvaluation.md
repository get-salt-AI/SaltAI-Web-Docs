---
tags:
- LLM
---

# ∞ Image Documents Evaluation
## Documentation
- Class name: `LLMMultiModalImageEvaluation`
- Category: `SALT/Language Toolkit/Querying`
- Output node: `False`

This node is designed to evaluate images based on a given query using a specified LLM model. It processes image paths or documents, loads the images, and sends them to the LLM model along with the query for evaluation, providing a text-based assessment of the images.
## Input types
### Required
- **`llm_model`**
    - The LLM model used for evaluating the images. It is crucial for performing the evaluation as it processes the images and the query to generate the evaluation result.
    - Comfy dtype: `LLM_MODEL`
    - Python dtype: `dict`
- **`image_documents`**
    - A list of image documents to be evaluated. These documents are processed and evaluated by the LLM model in conjunction with the query.
    - Comfy dtype: `DOCUMENT`
    - Python dtype: `list`
- **`llm_message`**
    - The query or message to be evaluated by the LLM model. This message guides the evaluation process of the images.
    - Comfy dtype: `LIST`
    - Python dtype: `list`
### Optional
- **`max_tokens`**
    - The maximum number of tokens to be generated by the LLM model during the evaluation. It limits the length of the evaluation result.
    - Comfy dtype: `INT`
    - Python dtype: `int`
## Output types
- **`response`**
    - Comfy dtype: `STRING`
    - The evaluation result text from the LLM model, providing a text-based assessment of the images.
    - Python dtype: `str`
## Usage tips
- Infra type: `GPU`
- Common nodes: unknown


## Source code
```python
class LLMMultiModalImageEvaluation:
    @classmethod
    def INPUT_TYPES(cls):
        return {
            "required": {
                "llm_model": ("LLM_MODEL",),
                "image_documents": ("DOCUMENT",),
                "llm_message": ("LIST",),
            },
            "optional": {
                "max_tokens": ("INT", {"min": 1, "max": 4096, "default": 1024})
            }
        }

    RETURN_TYPES = ("STRING", )
    RETURN_NAMES = ("response", )

    FUNCTION = "complete"
    CATEGORY = f"{MENU_NAME}/{SUB_MENU_NAME}/Querying"

    def complete(self, llm_model, image_documents, llm_message, max_tokens=1024):

        if not max_tokens or not isinstance(max_tokens, int):
            max_tokens = 1024

        model = llm_model.get("llm", None)

        if not model:
            raise ValueError("LLMMultiModalImageEvaluation unable to detect valid model")

        prompt = ""
        llm_message = sorted(llm_message, key=lambda message: message.role.value)
        for msg in llm_message:
            if isinstance(msg, ChatMessage) and msg.role == MessageRole.SYSTEM:
                if "SYSTEM:" not in prompt:
                    prompt += "SYSTEM: "
                prompt += msg.content + "\n\n"
            if isinstance(msg, ChatMessage) and msg.role == MessageRole.USER:
                if "USER:" not in prompt:
                    prompt += "USER: "
                prompt += msg.content + "\n\n"
            if isinstance(msg, str):
                prompt += msg + "\n\n"
        
        
        response = model.complete(
            prompt=prompt,
            image_documents=image_documents,
            max_tokens=max_tokens
        )

        return (response.text, )

```
