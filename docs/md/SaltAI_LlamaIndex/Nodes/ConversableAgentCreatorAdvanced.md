---
tags:
- Agents
- LLM
- LLMChat
---

# ∞ Conversable Agent (Adv)
## Documentation
- Class name: `ConversableAgentCreatorAdvanced`
- Category: `SALT/Shakers/Agents`
- Output node: `False`

This node facilitates the advanced creation of conversable agents, allowing for the customization of agent attributes such as name, system message, and optional parameters like LLM model, default auto reply, and a description. It's designed to enable the creation of more sophisticated and tailored conversational agents for various applications.
## Input types
### Required
- **`name`**
    - Specifies the name of the conversable agent, serving as a unique identifier and a way to personalize the agent.
    - Comfy dtype: `STRING`
    - Python dtype: `str`
- **`system_message`**
    - Defines the initial system message that the agent uses to guide or inform the user, setting the tone and context of the interaction.
    - Comfy dtype: `STRING`
    - Python dtype: `str`
### Optional
- **`llm_model`**
    - An optional parameter that allows for the specification of a language learning model to enhance the agent's conversational abilities.
    - Comfy dtype: `LLM_MODEL`
    - Python dtype: `dict`
- **`default_auto_reply`**
    - Provides a default automated reply for the agent when no specific code execution or LLM-based reply is generated.
    - Comfy dtype: `STRING`
    - Python dtype: `str`
- **`description`**
    - A brief description of the agent, used for identification or informational purposes by other agents.
    - Comfy dtype: `STRING`
    - Python dtype: `str`
## Output types
- **`agent`**
    - Comfy dtype: `AGENT`
    - The created conversable agent, ready for interaction and further customization.
    - Python dtype: `ConversableAgent`
## Usage tips
- Infra type: `CPU`
- Common nodes: unknown


## Source code
```python
class ConversableAgentCreatorAdvanced:
	@classmethod
	def INPUT_TYPES(cls):
		return {
			"required": {
				"name": ("STRING", {"multiline": False, "placeholder": "Assistant"}),
				"system_message": ("STRING", {
					"multiline": True,
					"default": "You are a helpful AI assistant. You can help with document QA. Return 'TERMINATE' when the task is done."
				}),
			},
			"optional": {
				"llm_model": ("LLM_MODEL",),
				# default auto reply when no code execution or llm-based reply is generated.
				"default_auto_reply": ("STRING", {"multiline": True}),
				# a short description of the agent, this description is used by other agents.
				"description": ("STRING", {"multiline": True}),
			}
		}

	RETURN_TYPES = ("AGENT",)
	RETURN_NAMES = ("agent",)

	FUNCTION = "create_agent"
	CATEGORY = "SALT/Shakers/Agents"

	def create_agent(self, name, system_message, llm_model=None, default_auto_reply="", description=None):
		agent = ConversableAgent(
			name=name,
			system_message=system_message,
			llm_config={"config_list": [{"model": llm_model["llm"].model, "api_key": llm_model["llm"].api_key}]} if llm_model is not None else False,
			human_input_mode="NEVER",
			default_auto_reply=default_auto_reply,
			description=description if description is not None else system_message,
		)
		return (agent,)

```
