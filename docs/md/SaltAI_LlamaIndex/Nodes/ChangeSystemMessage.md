---
tags:
- Agents
- LLM
- LLMChat
---

# ∞ Change System Message
## Documentation
- Class name: `ChangeSystemMessage`
- Category: `SALT/Llama-Index/Agents`
- Output node: `False`

The ChangeSystemMessage node is designed to modify the system message within an agent's configuration, allowing for dynamic updates to the instructions or information that the agent conveys to users.
## Input types
### Required
- **`agent`**
    - Represents the agent whose system message is to be updated, serving as the primary entity for modification.
    - Comfy dtype: `AGENT`
    - Python dtype: `ConversableAgent`
- **`system_message`**
    - The new system message to be set for the agent, defining the updated instructions or information it should convey.
    - Comfy dtype: `STRING`
    - Python dtype: `str`
## Output types
- **`agent`**
    - Comfy dtype: `AGENT`
    - The updated agent with the new system message applied, reflecting the changes made.
    - Python dtype: `ConversableAgent`
## Usage tips
- Infra type: `CPU`
- Common nodes: unknown


## Source code
```python
class ChangeSystemMessage:
	@classmethod
	def INPUT_TYPES(cls):
		return {
			"required": {
				"agent": ("AGENT",),
				"system_message": ("STRING", {
					"multiline": True,
					"default": "You are a helpful AI assistant. You can help with document QA. Return 'TERMINATE' when the task is done."
				}),
			},
		}

	RETURN_TYPES = ("AGENT",)
	RETURN_NAMES = ("agent",)

	FUNCTION = "update_system_prompt"
	CATEGORY = "SALT/Llama-Index/Agents"

	def update_system_prompt(self, agent, system_message, llm_model=None):
		agent = clone_conversable_agent(agent)
		agent.update_system_message(system_message)
		return (agent,)

```
