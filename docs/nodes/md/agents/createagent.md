# Create Agent

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Creates and returns a single agent configuration that defines the model, available tools, role, and system prompt. This configuration is intended to be passed into downstream nodes that run one or more agents.

</div>
<div style="flex: 0 0 300px;"><img src="../../../images/previews/agents/createagent.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node to define an agent before orchestrating multi-agent interactions. Typically, you will create one or more agents with different roles and prompts, then connect their outputs to a team/execution node (e.g., Run Agents Team) to perform collaborative tasks.

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
<tr><td style="word-wrap: break-word;">model</td><td>True</td><td style="word-wrap: break-word;">CHOICE</td><td style="word-wrap: break-word;">Select the LLM model the agent will use for inference. The available options are provided by the platform and may vary over time.</td><td style="word-wrap: break-word;">gpt-4o</td></tr>
<tr><td style="word-wrap: break-word;">tools</td><td>True</td><td style="word-wrap: break-word;">MULTICOMBO</td><td style="word-wrap: break-word;">Choose external tools the agent can access during execution. Enable one or more tools as needed for the agent's tasks.</td><td style="word-wrap: break-word;">{'Calculator': True, 'WebSearcher': True, 'WebScraper': False, 'DateTime': True}</td></tr>
<tr><td style="word-wrap: break-word;">role</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">A short description of the agent’s role. This is prepended to the system prompt and can help orchestrators select the right agent for a given task.</td><td style="word-wrap: break-word;">A meticulous research analyst specializing in market trends and competitor intelligence.</td></tr>
<tr><td style="word-wrap: break-word;">system_prompt</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The detailed system prompt guiding the agent’s behavior, tone, knowledge scope, and rules.</td><td style="word-wrap: break-word;">You are a helpful AI assistant. Provide concise, well-cited answers. When uncertain, ask clarifying questions before proceeding.</td></tr>
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
<tr><td style="word-wrap: break-word;">agent</td><td style="word-wrap: break-word;">AGENT</td><td style="word-wrap: break-word;">The constructed agent configuration containing the selected model, tools, role, and system prompt. Connect this to nodes that run agents or assemble agent teams.</td><td style="word-wrap: break-word;">{'model': 'gpt-4o', 'tools': {'Calculator': True, 'DateTime': True}, 'role': 'A helpful AI assistant.', 'system_prompt': 'You are a helpful AI assistant.'}</td></tr>
</tbody>
</table>
</div>

## Important Notes
- Role is prepended to the system prompt and may be used by orchestration logic to select or prioritize agents.
- Tools are optional per capability—enable only those necessary for the agent’s tasks to keep behavior focused.
- Model and tool options are provided by the platform and can change; verify selections if a workflow is reused later.

## Troubleshooting
- Selected model is missing: Reopen the model dropdown and choose an available model. Options may have changed since the workflow was created.
- Tools not taking effect: Ensure the specific tools are enabled (set to true) in the MULTICOMBO input and that the downstream node supports tool usage.
- Downstream node rejects the agent: Confirm the output from Create Agent is connected to an input expecting an AGENT type and that all required fields are set.
