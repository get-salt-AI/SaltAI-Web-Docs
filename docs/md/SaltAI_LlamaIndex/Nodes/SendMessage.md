---
tags:
- Agents
- LLM
- LLMChat
---

# ∞ Send Message
## Documentation
- Class name: `SendMessage`
- Category: `SALT/Llama-Index/Agents`
- Output node: `False`

The SendMessage node facilitates communication between agents by allowing one agent to send a message to another. It abstracts the complexities of agent interaction, ensuring messages are delivered and processed correctly, thereby enabling dynamic and interactive agent-based conversations.
## Input types
### Required
- **`recipient`**
    - The recipient agent who will receive the message. This parameter is crucial for directing the message to the intended agent, affecting the flow of conversation.
    - Comfy dtype: `AGENT`
    - Python dtype: `AGENT`
- **`sender`**
    - The sender agent who initiates the message. This parameter defines the origin of the message, playing a key role in the interaction between agents.
    - Comfy dtype: `AGENT`
    - Python dtype: `AGENT`
- **`message`**
    - The message content to be sent from the sender to the recipient. It supports multiline strings and defaults to 'Hi', allowing for flexible and varied agent communication.
    - Comfy dtype: `STRING`
    - Python dtype: `str`
## Output types
- **`recipient`**
    - Comfy dtype: `AGENT`
    - The recipient agent, potentially modified after receiving the message, indicating the state of the agent post-interaction.
    - Python dtype: `AGENT`
- **`sender`**
    - Comfy dtype: `AGENT`
    - The sender agent, potentially modified after sending the message, reflecting the agent's state post-interaction.
    - Python dtype: `AGENT`
## Usage tips
- Infra type: `CPU`
- Common nodes: unknown


## Source code
```python
class SendMessage:
	@classmethod
	def INPUT_TYPES(cls):
		return {
			"required": {
				"recipient": ("AGENT",),
				"sender": ("AGENT",),
				"message": ("STRING", {
					"multiline": True,
					"default": "Hi"
				}),
			},
		}

	RETURN_TYPES = ("AGENT", "AGENT")
	RETURN_NAMES = ("recipient", "sender")

	FUNCTION = "add_info"
	CATEGORY = "SALT/Llama-Index/Agents"

	def add_info(self, recipient, sender, message):
		recipient = clone_conversable_agent(recipient)
		sender = clone_conversable_agent(sender)
		sender.send(message, recipient)
		return (recipient, sender)

```
