---
tags:
- LLM
- LLMChat
---

# ∞ Group Chat
## Documentation
- Class name: `GroupChat`
- Category: `SALT/Llama-Index/Agents`
- Output node: `False`

The GroupChat node facilitates the creation and management of a group chat session involving multiple agents. It orchestrates the interaction between agents by initiating the chat, managing the flow of messages, and generating a summary of the conversation. This node is designed to handle the complexities of group communication, ensuring a seamless exchange of messages and the application of specific conversation rules or filters as defined.
## Input types
### Required
- **`group_manager`**
    - A configuration object for the group chat manager, detailing the management settings and parameters for the group chat session. It affects how the chat is initiated, managed, and concluded, including aspects like message filtering and speaker selection.
    - Comfy dtype: `GROUP_MANAGER`
    - Python dtype: `Dict[str, Any]`
- **`init_message`**
    - The initial message to start the group chat session, setting the tone and context for the conversation.
    - Comfy dtype: `STRING`
    - Python dtype: `str`
- **`send_introductions`**
    - Determines whether introductory messages are sent at the beginning of the chat, setting the stage for the conversation.
    - Comfy dtype: `BOOLEAN`
    - Python dtype: `bool`
- **`summary_method`**
    - The method used to generate a summary of the chat, encapsulating the key points or outcomes of the conversation.
    - Comfy dtype: `COMBO[STRING]`
    - Python dtype: `str`
- **`max_turns`**
    - The maximum number of turns the chat can take, defining the potential length and depth of the conversation.
    - Comfy dtype: `INT`
    - Python dtype: `int`
- **`clear_history`**
    - Indicates whether the chat history should be cleared at the start of the session, affecting the context and continuity of the conversation.
    - Comfy dtype: `BOOLEAN`
    - Python dtype: `bool`
### Optional
- **`agent_i`**
    - Represents one of the multiple agents participating in the group chat, contributing to the dynamic and interactive nature of the conversation.
    - Comfy dtype: `AGENT`
    - Python dtype: `CustomType[Agent]`
## Output types
- **`chat_history`**
    - Comfy dtype: `STRING`
    - A record of all messages exchanged during the group chat session, providing a comprehensive overview of the conversation.
    - Python dtype: `str`
- **`summary`**
    - Comfy dtype: `STRING`
    - A concise summary of the group chat, highlighting the main points or conclusions drawn from the conversation.
    - Python dtype: `str`
## Usage tips
- Infra type: `CPU`
- Common nodes: unknown


## Source code
```python
class GroupChat:
	@classmethod
	def INPUT_TYPES(cls):
		return {
			"required": {
				"group_manager": ("GROUP_MANAGER",),
				"init_message": ("STRING", {"multiline": True, "dynamicPrompts": False}),
				"send_introductions": ("BOOLEAN", {"default": False},),
				"summary_method": ([
					"last_msg",
					"reflection_with_llm",
				],),
				"max_turns": ("INT", {"default": 10}),
				"clear_history": ("BOOLEAN", {"default": True},),
			},
			"optional": {
				"agent_1": ("AGENT",),
				"agent_2": ("AGENT",),
				"agent_3": ("AGENT",),
				"agent_4": ("AGENT",),
				"agent_5": ("AGENT",),
				"agent_6": ("AGENT",),
				"agent_7": ("AGENT",),
				"agent_8": ("AGENT",),
			},
		}

	RETURN_TYPES = ("STRING", "STRING",)
	RETURN_NAMES = ("chat_history", "summary", )

	FUNCTION = "start_chat"
	CATEGORY = "SALT/Llama-Index/Agents"

	def start_chat(
		self,
		group_manager,
		init_message,
		send_introductions,
		summary_method,
		max_turns,
		clear_history,
		**kwargs,
	):
		agents = [kwargs[i] for i in kwargs if "agent_" in i]
		assert len(agents) > 1, "At least 2 agents are needed to start a group chat session"
		# create chat
		group_chat = AutogenGroupChat(
			agents=agents,
			messages=[],
			max_round=max_turns,
			send_introductions=send_introductions,
		)
		group_chat_manager = GroupChatManager(
			groupchat=group_chat,
			**group_manager,
		)
		# start chat
		chat_result = agents[0].initiate_chat(
			group_chat_manager,
			message=init_message,
			summary_method=summary_method,
			max_turns=max_turns,
			clear_history=clear_history,
		)
		# parse results
		summary = chat_result.summary
		chat_history = ""
		for message in chat_result.chat_history:
			if message["content"] is not None:
				chat_history += "- " + message["content"] + "\n\n"
		return (chat_history, summary)

```
