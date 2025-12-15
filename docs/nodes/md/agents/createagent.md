# Create Agent

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Builds an agent configuration object that specifies the model, tool access, role, and system prompt for an AI agent. It does not run any inference by itself; it prepares a reusable AGENT object for downstream nodes.

</div>
<div style="flex: 0 0 300px;"><img src="../../../images/previews/agents/createagent.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node to define how an agent should behave and what capabilities it has. Typically, you create one or more agents with different roles and tools, then pass them into a team or orchestrator node (e.g., Run Agents Team) to perform a collaborative task.

## Inputs

<div style="overflow-x: auto;">
<table style="table-layout: fixed; width: 100%;">
<colgroup>
<col style="width: 15%;">
<col style="width: 10%;">
<col style="width: 15%;">
<col style="width: 30%;">
<col style="width: 30%;">
</colgroup>
<thead><tr><th>Field</th><th>Required</th><th>Type</th><th>Description</th><th>Example</th></tr></thead>
<tbody>
<tr><td style="word-wrap: break-word;">model</td><td>True</td><td style="word-wrap: break-word;">CHOICE</td><td style="word-wrap: break-word;">LLM model the agent will use for inference. The available options are populated by the service and may vary over time.</td><td style="word-wrap: break-word;">gpt-4o</td></tr>
<tr><td style="word-wrap: break-word;">tools</td><td>True</td><td style="word-wrap: break-word;">MULTICOMBO</td><td style="word-wrap: break-word;">Select external tools the agent is allowed to call during reasoning (e.g., web search, scraping, calculation). Enable each tool you want the agent to have.</td><td style="word-wrap: break-word;">{'Calculator': True, 'DateTime': False, 'WebScraper': True}</td></tr>
<tr><td style="word-wrap: break-word;">role</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">A short description of the agent’s role. This is prepended to the system_prompt and also helps agent teams choose the best agent for a given task.</td><td style="word-wrap: break-word;">Expert research analyst focused on summarizing reliable web sources.</td></tr>
<tr><td style="word-wrap: break-word;">system_prompt</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The agent’s core system prompt that defines personality, domain expertise, constraints, and behavioral rules. Keep it concise and directive.</td><td style="word-wrap: break-word;">You are a careful, source-citing research assistant. Prefer authoritative sources, verify facts, and provide concise, structured answers.</td></tr>
</tbody>
</table>
</div>

## Outputs

<div style="overflow-x: auto;">
<table style="table-layout: fixed; width: 100%;">
<colgroup>
<col style="width: 20%;">
<col style="width: 20%;">
<col style="width: 35%;">
<col style="width: 25%;">
</colgroup>
<thead><tr><th>Field</th><th>Type</th><th>Description</th><th>Example</th></tr></thead>
<tbody>
<tr><td style="word-wrap: break-word;">agent</td><td style="word-wrap: break-word;">AGENT</td><td style="word-wrap: break-word;">An agent configuration object containing the selected model, tools, role, and system prompt. Feed this into nodes that execute or coordinate agents.</td><td style="word-wrap: break-word;">{'model': 'gpt-4o', 'tools': {'Calculator': True, 'WebScraper': True}, 'role': 'A helpful AI assistant.', 'system_prompt': 'You are a helpful AI assistant.'}</td></tr>
</tbody>
</table>
</div>

## Important Notes
- The node only creates an agent configuration; it does not execute any model calls by itself.
- The list of available models and tools is provided by the platform and can change; select from the shown options.
- The role text is prepended to the system prompt and is also used by agent teams to route tasks to the most appropriate agent.
- Tools are disabled by default; explicitly enable only those needed for the agent’s responsibilities.
- Use multiple Create Agent nodes to define specialized agents, then combine them in a team/orchestration node.

## Troubleshooting
- No models available in the dropdown: Refresh the workflow or check platform availability; then re-open the node to repopulate options.
- Selected tool not appearing: Ensure it is enabled (set to true) in the MULTICOMBO selection and that the tool is supported in your environment.
- Agent not behaving as expected: Refine the role and system_prompt. Keep instructions explicit, and ensure necessary tools are enabled.
- Downstream node rejects the agent: Confirm the downstream node expects an AGENT type and that all required fields (model, tools, role, system_prompt) are set.
