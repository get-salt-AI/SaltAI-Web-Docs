---
tags:
- LLM
- LLMChat
---

# ∞ Message (Advanced)
## Documentation
- Class name: `LLMChatMessagesAdv`
- Category: `SALT/Llama-Index/Messages`
- Output node: `False`

The LLMChatMessagesAdv node is designed to prepare chat messages by combining system and user prompts into a structured format suitable for further processing or interaction within a chat system.
## Input types
### Required
- **`system_prompt`**
    - The system prompt represents the initial message or context provided by the system, setting the stage for the chat interaction.
    - Comfy dtype: `STRING`
    - Python dtype: `str`
- **`user_prompt`**
    - The user prompt captures the user's input or response to the system's initial message, facilitating a two-way conversation.
    - Comfy dtype: `STRING`
    - Python dtype: `str`
## Output types
- **`llm_message`**
    - Comfy dtype: `LIST`
    - This output is a list of chat messages structured to include both system and user contributions, ready for use in chat-based applications.
    - Python dtype: `List[ChatMessage]`
## Usage tips
- Infra type: `CPU`
- Common nodes: unknown


## Source code
```python
class LLMChatMessagesAdv:
    def __init__(self):
        pass

    @classmethod
    def INPUT_TYPES(cls):
        return {
            "required": {
                "system_prompt": ("STRING", {"multiline": True, "dynamicPrompts": False, "placeholder": "You are a dog, you cannot speak, only woof, and react as a dog would."}),
                "user_prompt": ("STRING", {"multiline": True, "dynamicPrompts": False, "plaeholder": "What is your name?"}),
            },
        }

    RETURN_TYPES = ("LIST", )
    RETURN_NAMES = ("llm_message", )

    FUNCTION = "prepare_messages"
    CATEGORY = "SALT/Llama-Index/Messages"

    def prepare_messages(self, system_prompt, user_prompt):
        messages = [
                ChatMessage(role=MessageRole.SYSTEM, content=system_prompt ),
                ChatMessage(role=MessageRole.USER, content=user_prompt ),
        ]
        return (messages,)

```
