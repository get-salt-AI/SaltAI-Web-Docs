---
tags:
- Agents
- LLM
- LLMChat
---

# ∞ Generate Reply
## Documentation
- Class name: `GenerateReply`
- Category: `SALT/Llama-Index/Agents`
- Output node: `False`

The GenerateReply node is designed to simulate a conversational interaction by generating a reply based on a given message and recipient, optionally considering the sender. It encapsulates the complexity of conversational dynamics within agents, facilitating the creation of interactive dialogues.
## Input types
### Required
- **`recipient`**
    - The recipient agent who will receive the message. This agent's perspective is used to generate the reply, making it a crucial part of the conversational context.
    - Comfy dtype: `AGENT`
    - Python dtype: `ConversableAgent`
- **`message`**
    - The message to which the recipient agent is expected to reply. This string input sets the context for the generated response.
    - Comfy dtype: `STRING`
    - Python dtype: `str`
### Optional
- **`sender`**
    - The sender agent, optional for providing additional context. If specified, it influences the reply by adding another layer of interaction dynamics.
    - Comfy dtype: `AGENT`
    - Python dtype: `ConversableAgent`
## Output types
- **`reply`**
    - Comfy dtype: `STRING`
    - The generated reply from the recipient to the given message, reflecting the conversational agent's response.
    - Python dtype: `str`
## Usage tips
- Infra type: `CPU`
- Common nodes: unknown


## Source code
```python
class GenerateReply:
	@classmethod
	def INPUT_TYPES(cls):
		return {
			"required": {
				"recipient": ("AGENT",),
				"message": ("STRING", {"multiline": True, "dynamicPrompts": False}),
			},
			"optional": {
				"sender": ("AGENT", {"default": None}),
			}
		}

	RETURN_TYPES = ("STRING",)
	RETURN_NAMES = ("reply",)

	FUNCTION = "start_chat"
	CATEGORY = "SALT/Llama-Index/Agents"

	def start_chat(self, recipient, message, sender=None):
		recipient = clone_conversable_agent(recipient)
		sender = clone_conversable_agent(sender)
		message = recipient._oai_messages[sender] + [{"content": message, "role": "user"}]
		reply = recipient.generate_reply(message, sender)
		return (reply,)

```
