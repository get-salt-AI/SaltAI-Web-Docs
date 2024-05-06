---
tags:
- LLM
- LLMChat
---

# ∞ Messages Concat
## Documentation
- Class name: `LLMChatMessageConcat`
- Category: `SALT/Llama-Index/Messages`
- Output node: `False`

The LLMChatMessageConcat node is designed to concatenate two lists of chat messages, effectively merging the conversation histories from two sources into a single, unified sequence. This functionality is crucial for maintaining a coherent and continuous chat history in applications that involve complex dialog flows or require the integration of multiple chat sessions.
## Input types
### Required
- **`message_a`**
    - Represents the first list of chat messages to be concatenated. It plays a critical role in the merging process by serving as the initial segment of the new, combined chat history.
    - Comfy dtype: `LIST`
    - Python dtype: `List[ChatMessage]`
- **`message_b`**
    - Denotes the second list of chat messages to be concatenated with the first. Its inclusion allows for the seamless extension of the chat history, ensuring that no part of the conversation is lost in the process.
    - Comfy dtype: `LIST`
    - Python dtype: `List[ChatMessage]`
## Output types
- **`llm_message`**
    - Comfy dtype: `LIST`
    - The result of concatenating `message_a` and `message_b`, providing a single, continuous list of chat messages that represents the merged chat history.
    - Python dtype: `List[ChatMessage]`
## Usage tips
- Infra type: `CPU`
- Common nodes: unknown


## Source code
```python
class LLMChatMessageConcat:
    def __init__(self):
        pass
    
    @classmethod
    def INPUT_TYPES(cls):
        return {
            "required": {
                "message_a": ("LIST", ),
                "message_b": ("LIST", ),
            },
        }

    RETURN_TYPES = ("LIST",)
    RETURN_NAMES = ("llm_message", )

    FUNCTION = "concat_messages"
    CATEGORY = "SALT/Llama-Index/Messages"

    def concat_messages(self, message_a, message_b):
        return (message_a + message_b, )

```
