---
tags:
- LLM
- LLMChat
---

# ∞ Message
## Documentation
- Class name: `LLMChatMessages`
- Category: `SALT/Llama-Index/Messages`
- Output node: `False`

The LLMChatMessages node is designed to prepare and structure chat messages for interaction between a system and a user. It encapsulates the process of creating chat messages with specific roles, ensuring that the dialogue flow is maintained according to the given prompts.
## Input types
### Required
- **`prompt`**
    - unknown
    - Comfy dtype: `STRING`
    - Python dtype: `unknown`
- **`role`**
    - unknown
    - Comfy dtype: `COMBO[STRING]`
    - Python dtype: `unknown`
## Output types
- **`llm_message`**
    - Comfy dtype: `LIST`
    - This output is a list of chat messages structured for further processing or display, encapsulating both the system's and the user's contributions to the conversation.
    - Python dtype: `List[ChatMessage]`
## Usage tips
- Infra type: `CPU`
- Common nodes: unknown


## Source code
```python
class LLMChatMessages:
    def __init__(self):
        pass

    @classmethod
    def INPUT_TYPES(cls):
        return {
            "required": {
                "prompt": ("STRING", {"multiline": True, "dynamicPrompts": False, "default": "prompt"}),
                "role": (["SYSTEM", "USER"],),
            },
        }

    RETURN_TYPES = ("LIST", )
    RETURN_NAMES = ("llm_message", )

    FUNCTION = "prepare_messages"
    CATEGORY = "SALT/Llama-Index/Messages"

    def prepare_messages(self, prompt, role):
        messages = [
                ChatMessage(role=MessageRole.SYSTEM if role == "SYSTEM" else MessageRole.USER, content=prompt ),
        ]
        return (messages,)

```
