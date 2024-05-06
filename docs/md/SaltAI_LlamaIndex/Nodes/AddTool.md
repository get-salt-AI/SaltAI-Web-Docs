---
tags:
- Agents
- LLM
- LLMChat
---

# ∞ Add Tool
## Documentation
- Class name: `AddTool`
- Category: `SALT/Llama-Index/Agents`
- Output node: `False`

The AddTool node is designed to enhance conversational agents by registering new functionalities or tools to them. It focuses on expanding the capabilities of agents, allowing them to perform additional tasks or services beyond their initial configuration.
## Input types
### Required
- **`tool`**
    - The tool parameter represents the functionality to be added to the agent, including its name, function, and description. It is crucial for defining the new capability that will be registered with the agent.
    - Comfy dtype: `TOOL`
    - Python dtype: `Dict[str, Any]`
- **`assistant`**
    - The assistant parameter refers to the conversational agent that will receive the new tool. This agent is cloned and modified to include the new functionality.
    - Comfy dtype: `AGENT`
    - Python dtype: `AGENT`
### Optional
- **`executor`**
    - The executor parameter optionally specifies a different agent to execute the tool's function, allowing for flexibility in tool assignment between agents.
    - Comfy dtype: `AGENT`
    - Python dtype: `AGENT`
## Output types
- **`assistant`**
    - Comfy dtype: `AGENT`
    - The modified conversational agent with the new tool registered.
    - Python dtype: `AGENT`
- **`executor`**
    - Comfy dtype: `AGENT`
    - The agent designated to execute the new tool, which may be the same as or different from the assistant.
    - Python dtype: `AGENT`
## Usage tips
- Infra type: `CPU`
- Common nodes: unknown


## Source code
```python
class AddTool:
    @classmethod
    def INPUT_TYPES(cls):
        return {
            "required": {
                "tool": ("TOOL",),
                "assistant": ("AGENT",),
            },
            "optional": {
                "executor": ("AGENT",),
            }
        }

    RETURN_TYPES = ("AGENT", "AGENT",)
    RETURN_NAMES = ("assistant", "executor",)

    FUNCTION = "create_tool"
    CATEGORY = "SALT/Llama-Index/Agents"

    def create_tool(self, tool, assistant, executor=None):
        assistant = clone_conversable_agent(assistant)
        if executor is None:
            executor = assistant
        # Register the calculator function to the two agents.
        register_function(
            tool["function"],
            caller=assistant,
            executor=executor,
            name=tool["name"],
            description=tool["description"],
        )
        return (assistant, executor,)

```
