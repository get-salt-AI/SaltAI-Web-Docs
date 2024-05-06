---
tags:
- LLM
- LLMChat
---

# ∞ Documents Chat Engine
## Documentation
- Class name: `LLMChatEngine`
- Category: `SALT/Llama-Index/Querying`
- Output node: `False`

The LLMChatEngine node facilitates querying a language model index to generate responses based on user queries. It supports initializing or resetting the underlying chat engine to ensure fresh or continuous conversation contexts.
## Input types
### Required
- **`llm_index`**
    - Represents the language model index used for generating chat responses. It is crucial for initializing the chat engine or resetting it to a new state.
    - Comfy dtype: `LLM_INDEX`
    - Python dtype: `LLM_INDEX`
- **`query`**
    - The user's query or question to the language model. It drives the conversation by providing context for the model's response.
    - Comfy dtype: `STRING`
    - Python dtype: `str`
### Optional
- **`reset_engine`**
    - A flag to reset the chat engine, allowing for a fresh start of the conversation. Useful for clearing previous contexts or errors.
    - Comfy dtype: `BOOLEAN`
    - Python dtype: `bool`
## Output types
- **`string`**
    - Comfy dtype: `STRING`
    - The generated response from the chat engine based on the user's query.
    - Python dtype: `str`
## Usage tips
- Infra type: `CPU`
- Common nodes: unknown


## Source code
```python
class LLMChatEngine:
        def __init__(self):
            self.chat_engine = None
            
        @classmethod
        def INPUT_TYPES(cls):
            return {
                "required": {
                    "llm_index": ("LLM_INDEX",),
                    "query": ("STRING", {"multiline": True, "dynamicPrompts": False, "placeholder": "Ask a question"}),
                },
                "optional": {
                    "reset_engine": ("BOOLEAN", {"default": False})
                }
            }

        RETURN_TYPES = ("STRING",)
        FUNCTION = "chat"
        CATEGORY = "SALT/Llama-Index/Querying"

        def chat(self, llm_index, query:str, reset_engine:bool = False) -> str:
            if not self.chat_engine or reset_engine:
                self.chat_engine = llm_index.as_chat_engine()
            response = self.chat_engine.chat(query)
            pprint(response, indent=4)
            return (response.response,)

```
