---
tags:
- Agents
- LLM
- LLMChat
---

# ∞ Conversable Agent
## Documentation
- Class name: `ConversableAgentCreator`
- Category: `SALT/Shakers/Agents`
- Output node: `False`

This node facilitates the creation of conversable agents, which are AI entities designed to assist with document QA and other tasks by processing system messages and optionally integrating with large language models (LLMs).
## Input types
### Required
- **`name`**
    - Specifies the name of the conversable agent, serving as a unique identifier and a human-readable reference.
    - Comfy dtype: `STRING`
    - Python dtype: `str`
- **`system_message`**
    - Defines the initial system message that sets the context for the agent's operation, guiding its responses and interactions.
    - Comfy dtype: `STRING`
    - Python dtype: `str`
### Optional
- **`llm_model`**
    - Optionally integrates a large language model (LLM) configuration to enhance the agent's capabilities with advanced language understanding and generation.
    - Comfy dtype: `LLM_MODEL`
    - Python dtype: `Optional[Dict[str, Any]]`
## Output types
- **`agent`**
    - Comfy dtype: `AGENT`
    - The created conversable agent, ready for interaction and further configuration.
    - Python dtype: `ConversableAgent`
## Usage tips
- Infra type: `CPU`
- Common nodes: unknown


## Source code
```python
class ConversableAgentCreator:
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
			}
		}

	RETURN_TYPES = ("AGENT",)
	RETURN_NAMES = ("agent",)

	FUNCTION = "create_agent"
	CATEGORY = "SALT/Shakers/Agents"

	def create_agent(self, name, system_message, llm_model=None):
		agent = ConversableAgent(
			name=name,
			system_message=system_message,
			llm_config={"config_list": [{"model": llm_model["llm"].model, "api_key": llm_model["llm"].api_key}]} if llm_model is not None else False,
			human_input_mode="NEVER",
		)
		return (agent,)

```
