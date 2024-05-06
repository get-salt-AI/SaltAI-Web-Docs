---
tags:
- LLM
- LLMChat
---

# ∞ Group Chat (Adv)
## Documentation
- Class name: `GroupChatAdvanced`
- Category: `SALT/Llama-Index/Agents`
- Output node: `False`

This node facilitates advanced group chat functionalities, enabling the orchestration of complex interactions among multiple agents. It leverages a sophisticated set of parameters to manage group dynamics, speaker selection, and conversation flow, aiming to simulate realistic group conversations with customizable settings.
## Input types
### Required
- **`group_manager`**
    - Represents the configuration and management entity for the group chat, including settings for the chat's behavior and dynamics.
    - Comfy dtype: `GROUP_MANAGER`
    - Python dtype: `Dict[str, Union[str, int, bool, Dict[str, Union[str, List[Dict[str, str]]]]]]`
- **`init_message`**
    - The initial message to kick off the group chat, setting the tone and context for the conversation.
    - Comfy dtype: `STRING`
    - Python dtype: `str`
- **`select_speaker_message_template`**
    - Template for messages used in selecting the next speaker, allowing for dynamic speaker selection based on conversation context.
    - Comfy dtype: `STRING`
    - Python dtype: `str`
- **`select_speaker_prompt_template`**
    - Template for prompts used in selecting the next speaker, guiding the speaker selection process with specific criteria or questions.
    - Comfy dtype: `STRING`
    - Python dtype: `str`
- **`summary_method`**
    - Specifies the method used to summarize the chat, providing an overview or key takeaways from the conversation.
    - Comfy dtype: `COMBO[STRING]`
    - Python dtype: `str`
- **`max_turns`**
    - The maximum number of turns the chat can take, limiting the length of the conversation.
    - Comfy dtype: `INT`
    - Python dtype: `int`
- **`func_call_filter`**
    - A filter to control which function calls are allowed during the chat, ensuring that only appropriate interactions occur. It affects the node's execution by filtering potential speaker selections based on the presence of corresponding function names in agents' function maps.
    - Comfy dtype: `BOOLEAN`
    - Python dtype: `bool`
- **`speaker_selection_method`**
    - The method used to select the next speaker in the group chat, determining how speakers are rotated or chosen.
    - Comfy dtype: `COMBO[STRING]`
    - Python dtype: `str`
- **`allow_repeat_speaker`**
    - Determines whether a speaker can be selected consecutively, affecting the diversity of speaker turns.
    - Comfy dtype: `BOOLEAN`
    - Python dtype: `bool`
- **`send_introductions`**
    - Controls whether introductory messages are sent for each agent, setting the stage for their participation in the chat.
    - Comfy dtype: `BOOLEAN`
    - Python dtype: `bool`
- **`role_for_select_speaker_messages`**
    - Defines the role (e.g., system or user) assigned to messages involved in selecting the next speaker, influencing how these messages are perceived.
    - Comfy dtype: `COMBO[STRING]`
    - Python dtype: `str`
- **`clear_history`**
    - Indicates whether the chat history should be cleared before starting, affecting the context available to agents.
    - Comfy dtype: `BOOLEAN`
    - Python dtype: `bool`
### Optional
- **`agent_i`**
    - Represents one of the multiple agents participating in the group chat, contributing to the dynamic interaction and conversation flow.
    - Comfy dtype: `AGENT`
    - Python dtype: `Dict[str, Any]`
## Output types
- **`chat_history`**
    - Comfy dtype: `STRING`
    - A record of all messages exchanged during the group chat, capturing the flow and content of the conversation.
    - Python dtype: `str`
- **`summary`**
    - Comfy dtype: `STRING`
    - A summary of the chat, highlighting key points or outcomes from the group interaction.
    - Python dtype: `str`
## Usage tips
- Infra type: `CPU`
- Common nodes: unknown


## Source code
```python
class GroupChatAdvanced:
	@classmethod
	def INPUT_TYPES(cls):
		return {
			"required": {
				"group_manager": ("GROUP_MANAGER",),
				"init_message": ("STRING", {"multiline": True, "dynamicPrompts": False}),
				# select_speaker_message_template: customize the select speaker message (used in "auto" speaker selection), which appears first in the message context and generally includes the agent descriptions and list of agents. The string value will be converted to an f-string, use "{roles}" to output the agent's and their role descriptions and "{agentlist}" for a comma-separated list of agent names in square brackets.
				"select_speaker_message_template": ("STRING", {
					"multiline": True,
					"dynamicPrompts": False,
					"default": "You are in a role play game. The following roles are available:\n{roles}.\nRead the following conversation.\nThen select the next role from {agentlist} to play. Only return the role."
				}),
				# select_speaker_prompt_template: customize the select speaker prompt (used in "auto" speaker selection), which appears last in the message context and generally includes the list of agents and guidance for the LLM to select the next agent. The string value will be converted to an f-string, use "{agentlist}" for a comma-separated list of agent names in square brackets.
				"select_speaker_prompt_template": ("STRING", {
					"multiline": True,
					"dynamicPrompts": False,
					"default": "Read the above conversation. Then select the next role from {agentlist} to play. Only return the role."
				}),
				"summary_method": ([
					"last_msg",
					"reflection_with_llm",
				],),
				"max_turns": ("INT", {"default": 10}),
				# When set to True and when a message is a function call suggestion,
				# the next speaker will be chosen from an agent which contains the corresponding function name
				# in its `function_map`
				"func_call_filter": ("BOOLEAN", {"default": True},),
				# speaker_selection_method: the method for selecting the next speaker.
				# Could be any of the following (case insensitive), will raise ValueError if not recognized:
				# - "auto": the next speaker is selected automatically by LLM.
				# - "manual": the next speaker is selected manually by user input.
				# - "random": the next speaker is selected randomly.
				# - "round_robin": the next speaker is selected in a round robin fashion, i.e., iterating in the same order as provided in `agents`.
				"speaker_selection_method": ([
					"auto",
					# "manual",
					"random",
					"round_robin",
				],),
				# whether to allow the same speaker to speak consecutively.
				"allow_repeat_speaker": ("BOOLEAN", {"default": True},),
				# send_introductions: send a round of introductions at the start of the group chat, so agents know who they can speak to
				"send_introductions": ("BOOLEAN", {"default": False},),
				# role_for_select_speaker_messages: sets the role name for speaker selection when in 'auto' mode, typically 'user' or 'system'.
				"role_for_select_speaker_messages": ([
					"system",
					"user",
				],),
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
		select_speaker_message_template,
		select_speaker_prompt_template,
		func_call_filter,
		speaker_selection_method,
		allow_repeat_speaker,
		send_introductions,
		role_for_select_speaker_messages,
		summary_method,
		max_turns,
		clear_history,
		**kwargs,
	):
		agents = [kwargs[i] for i in kwargs if "agent_" in i]
		assert len(agents) != 1, "At least 1 agent is needed to start a group chat session"
		# create chat
		group_chat = AutogenGroupChat(
			agents=agents,
			messages=[],
			max_round=max_turns,
			func_call_filter=func_call_filter,
			select_speaker_prompt_template=select_speaker_prompt_template,
			speaker_selection_method=speaker_selection_method,
			allow_repeat_speaker=allow_repeat_speaker,
			send_introductions=send_introductions,
			role_for_select_speaker_messages=role_for_select_speaker_messages,
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
			chat_history += "- " + str(message["content"]) + "\n\n"
		return (chat_history, summary)

```
