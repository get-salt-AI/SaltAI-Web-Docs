---
tags:
- LLM
- LLMChat
---

# ∞ Simple Chat
## Documentation
- Class name: `SimpleChat`
- Category: `SALT/Llama-Index/Agents`
- Output node: `False`

The SimpleChat node facilitates the initiation and management of a chat session between two agents, incorporating options for chat history management and summary generation methods. It abstracts the complexities of chat initiation, message exchange, and summarization, providing a streamlined interface for creating interactive chat experiences.
## Input types
### Required
- **`initiator`**
    - Specifies the initiating agent in the chat, playing a crucial role in starting the conversation.
    - Comfy dtype: `AGENT`
    - Python dtype: `object`
- **`recipient`**
    - Defines the recipient agent in the chat, who will respond to the initiator's messages.
    - Comfy dtype: `AGENT`
    - Python dtype: `object`
- **`summary_method`**
    - Determines the method used for generating a summary of the chat, affecting the overall understanding and conclusion of the chat session.
    - Comfy dtype: `COMBO[STRING]`
    - Python dtype: `str`
- **`clear_history`**
    - Indicates whether to clear the chat history with the agent before starting a new session, impacting the continuity of the conversation.
    - Comfy dtype: `BOOLEAN`
    - Python dtype: `bool`
### Optional
- **`init_message`**
    - The initial message to start the chat with, setting the tone and context of the conversation.
    - Comfy dtype: `STRING`
    - Python dtype: `str`
- **`max_turns`**
    - Limits the number of turns in the chat, controlling the length and depth of the conversation.
    - Comfy dtype: `INT`
    - Python dtype: `int`
## Output types
- **`chat_history`**
    - Comfy dtype: `STRING`
    - The complete history of messages exchanged during the chat session.
    - Python dtype: `str`
- **`summary`**
    - Comfy dtype: `STRING`
    - A summary of the chat, providing insights or a concise overview of the conversation.
    - Python dtype: `str`
- **`initiator`**
    - Comfy dtype: `AGENT`
    - The initiating agent, returned post-chat for potential further interactions.
    - Python dtype: `object`
- **`recipient`**
    - Comfy dtype: `AGENT`
    - The recipient agent, returned post-chat for potential further interactions.
    - Python dtype: `object`
## Usage tips
- Infra type: `CPU`
- Common nodes: unknown


## Source code
```python
class SimpleChat:
	@classmethod
	def INPUT_TYPES(cls):
		return {
			"required": {
				"initiator": ("AGENT",),
				"recipient": ("AGENT",),
				"summary_method": ([
					"last_msg",
					"reflection_with_llm",
				],),
				# whether to clear the chat history with the agent
				"clear_history": ("BOOLEAN", {"default": True},),
			},
			"optional": {
				"init_message": ("STRING", {"multiline": True, "dynamicPrompts": False}),
				"max_turns": ("INT", {"default": 10}),
			}
		}

	RETURN_TYPES = ("STRING", "STRING", "AGENT", "AGENT",)
	RETURN_NAMES = ("chat_history", "summary", "initiator", "recipient",)

	FUNCTION = "start_chat"
	CATEGORY = "SALT/Llama-Index/Agents"

	def start_chat(self, initiator, recipient, summary_method, clear_history, init_message=None, max_turns=None):
		initiator = clone_conversable_agent(initiator)
		recipient = clone_conversable_agent(recipient)

		chat_result = initiator.initiate_chat(
			recipient,
			message=init_message,
			max_turns=max_turns,
			summary_method=summary_method,
			clear_history=clear_history,
		)
		summary = chat_result.summary
		chat_history = ""
		for message in chat_result.chat_history:
			if message["content"] is not None:
				chat_history += "- " + message["content"] + "\n\n"
		return (chat_history, summary, initiator, recipient,)

```
