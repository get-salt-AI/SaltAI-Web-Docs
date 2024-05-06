---
tags:
- Agents
- LLM
- LLMChat
---

# ∞ Convert Agent To Llamaindex
## Documentation
- Class name: `ConvertAgentToLlamaindex`
- Category: `SALT/Shakers/Agents`
- Output node: `False`

This node is designed to transform an existing agent into a format compatible with the Llamaindex, enabling its integration into the Llamaindex ecosystem for further processing or utilization.
## Input types
### Required
- **`agent`**
    - The agent to be converted into a Llamaindex-compatible format. This is the primary entity undergoing transformation.
    - Comfy dtype: `AGENT`
    - Python dtype: `dict`
### Optional
- **`optional_embed_model`**
    - An optional embedding model to enhance the agent's conversion process, providing additional context or capabilities.
    - Comfy dtype: `LLM_EMBED_MODEL`
    - Python dtype: `dict`
## Output types
- **`model`**
    - Comfy dtype: `LLM_MODEL`
    - The transformed agent, now in a format compatible with the Llamaindex, ready for integration or further use.
    - Python dtype: `dict`
## Usage tips
- Infra type: `CPU`
- Common nodes: unknown


## Source code
```python
class ConvertAgentToLlamaindex:
	@classmethod
	def INPUT_TYPES(cls):
		return {
			"required": {
				"agent": ("AGENT",),
			},
			"optional": {
				"optional_embed_model": ("LLM_EMBED_MODEL",)
            }
		}

	RETURN_TYPES = ("LLM_MODEL",)
	RETURN_NAMES = ("model",)

	FUNCTION = "convert_agent"
	CATEGORY = "SALT/Shakers/Agents"

	def convert_agent(self, agent, optional_embed_model=None):
		llm = {"llm": BaseModel(agent)}
		if optional_embed_model:
			llm.update(optional_embed_model)
		return (llm,)

```
