---
tags:
- LLM
- LLMChat
---

# ∞ Group Chat Manager
## Documentation
- Class name: `GroupChatManagerCreator`
- Category: `SALT/Llama-Index/Agents`
- Output node: `False`

The GroupChatManagerCreator node is designed to facilitate the creation of a group chat manager, specifically tailored for managing group chats with multiple agents. It encapsulates the configuration and initialization of a group chat manager, ensuring that the chat environment is optimized for group interactions.
## Input types
### Required
- **`name`**
    - The 'name' parameter specifies the name of the group chat manager, serving as an identifier within the chat environment.
    - Comfy dtype: `STRING`
    - Python dtype: `str`
- **`system_message`**
    - The 'system_message' parameter is used to define a message that represents the system within the group chat, often used for announcements or system notifications.
    - Comfy dtype: `STRING`
    - Python dtype: `str`
### Optional
- **`llm_model`**
    - The 'llm_model' parameter allows for the specification of a language model to be used by the group chat manager, enabling advanced conversational capabilities.
    - Comfy dtype: `LLM_MODEL`
    - Python dtype: `dict`
- **`max_consecutive_auto_reply`**
    - This parameter sets the maximum number of consecutive automatic replies the group chat manager can send without human intervention, controlling the flow of automated responses.
    - Comfy dtype: `INT`
    - Python dtype: `int`
## Output types
- **`group_manager`**
    - Comfy dtype: `GROUP_MANAGER`
    - The output is a configured group chat manager, ready to be utilized in managing a group chat with multiple agents.
    - Python dtype: `dict`
## Usage tips
- Infra type: `CPU`
- Common nodes: unknown


## Source code
```python
class GroupChatManagerCreator:
	"""
	A chat manager agent that can manage a group chat of multiple agents.
	Can only be used in group chats.
	"""
	@classmethod
	def INPUT_TYPES(cls):
		return {
			"required": {
				"name": ("STRING", {"multiline": False, "placeholder": "Manager"}),
				"system_message": ("STRING", {
					"multiline": True,
					"placeholder": "Group chat manager.",
				}),
			},
			"optional": {
				"llm_model": ("LLM_MODEL",),
				"max_consecutive_auto_reply": ("INT", {"default": 10}),
			}
		}

	RETURN_TYPES = ("GROUP_MANAGER",)
	RETURN_NAMES = ("group_manager",)

	FUNCTION = "create_agent"
	CATEGORY = "SALT/Llama-Index/Agents"

	def create_agent(self, name, system_message, llm_model=None, max_consecutive_auto_reply=None):
		group_manager = {
			"name": name,
			"system_message": system_message,
			"llm_config": {
				"config_list": [
					{
						"model": llm_model["llm"].model,
						"api_key": llm_model["llm"].api_key,
					}
				]
			} if llm_model is not None else None,
			"max_consecutive_auto_reply": max_consecutive_auto_reply,
		}
		return (group_manager,)

```
