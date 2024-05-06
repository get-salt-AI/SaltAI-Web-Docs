---
tags:
- Agents
- LLM
- LLMChat
---

# ∞ Agent As Tool
## Documentation
- Class name: `ConvertAgentAsTool`
- Category: `SALT/Llama-Index/Agents`
- Output node: `False`

This node transforms a conversational agent into a tool that can be invoked with a specific message, facilitating the creation of dynamic, agent-powered functionalities within applications.
## Input types
### Required
- **`agent`**
    - The conversational agent to be transformed into a tool. This agent's capabilities are encapsulated as a callable function within the tool, allowing it to generate responses based on input messages.
    - Comfy dtype: `AGENT`
    - Python dtype: `ConversableAgent`
## Output types
- **`tool`**
    - Comfy dtype: `TOOL`
    - A tool encapsulating the conversational agent's functionality, enabling it to be called with messages and generate responses.
    - Python dtype: `Dict[str, Any]`
## Usage tips
- Infra type: `CPU`
- Common nodes: unknown


## Source code
```python
class ConvertAgentAsTool:
    @classmethod
    def INPUT_TYPES(cls):
        return {
            "required": {
                "agent": ("AGENT",),
            },
        }

    RETURN_TYPES = ("TOOL",)
    RETURN_NAMES = ("tool",)

    FUNCTION = "create_tool"
    CATEGORY = "SALT/Llama-Index/Agents"

    def create_tool(self, agent):
        agent = clone_conversable_agent(agent)
        def agent_call(message: Annotated[str, "Message to ask the assistant."]):
            return agent.generate_reply(messages=[{"content": message, "role": "user"}])

        tool = {
            "name": "agent_call",
            "description": agent.description,
            "function": agent_call,
        }
        return (tool,)

```
