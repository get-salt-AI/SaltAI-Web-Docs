---
tags:
- Cache
---

# ∞ Clear Memory
## Documentation
- Class name: `ClearMemory`
- Category: `SALT/Llama-Index/Agents`
- Output node: `False`

The ClearMemory node is designed to manage and clear the memory history of an agent, potentially improving performance and freeing up resources by removing historical data that is no longer needed.
## Input types
### Required
- **`agent`**
    - Specifies the agent whose memory history is to be cleared, targeting its stored data for removal.
    - Comfy dtype: `AGENT`
    - Python dtype: `ConversableAgent`
## Output types
- **`agent`**
    - Comfy dtype: `AGENT`
    - Returns the agent with its memory history cleared, reflecting the removal of specified data.
    - Python dtype: `ConversableAgent`
## Usage tips
- Infra type: `CPU`
- Common nodes: unknown


## Source code
```python
class ClearMemory:
	@classmethod
	def INPUT_TYPES(cls):
		return {
			"required": {
				"agent": ("AGENT",),
			},
			"oprional": {
				"recipient": ("AGENT", {"default": None}),
			},
		}

	RETURN_TYPES = ("AGENT",)
	RETURN_NAMES = ("agent",)

	FUNCTION = "clear_memory"
	CATEGORY = "SALT/Llama-Index/Agents"

	def clear_memory(self, agent, recipient=None):
		agent = clone_conversable_agent(agent)
		agent.clear_history(recipient)
		return (agent,)

```
