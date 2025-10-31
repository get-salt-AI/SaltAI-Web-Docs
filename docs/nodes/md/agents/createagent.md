# Create Agent

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Builds a reusable agent configuration object by selecting a language model, enabling external tools, and defining the agent’s role and system prompt. It does not run any inference; it simply packages the provided settings into an AGENT output to be consumed by downstream nodes.

</div>
<div style="flex: 0 0 300px;"><img src="../../../images/previews/agents/createagent.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need to define one or more agents for collaborative or managed multi-agent workflows. Configure the model, choose which tools the agent can use, and set its role and behavior. Connect the AGENT output to nodes that execute or coordinate agents (e.g., team orchestration).

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
<tr><td style="word-wrap: break-word;">model</td><td>True</td><td style="word-wrap: break-word;">ENUM</td><td style="word-wrap: break-word;">Select the LLM model the agent will use for inference. The available list is fetched from the Agents service; if unavailable, a fallback list is used.</td><td style="word-wrap: break-word;">gpt-4o</td></tr>
<tr><td style="word-wrap: break-word;">tools</td><td>True</td><td style="word-wrap: break-word;">MULTICOMBO</td><td style="word-wrap: break-word;">Choose which external tools the agent can access. Each selected tool will be enabled for this agent. Defaults to all tools disabled.</td><td style="word-wrap: break-word;">{"Calculator": true, "WebSearcher": true, "DateTime": false}</td></tr>
<tr><td style="word-wrap: break-word;">role</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Short description of the agent’s role. Prepended to the system prompt and used by agent teams to pick the best agent for a task.</td><td style="word-wrap: break-word;">A focused research assistant skilled at finding and validating sources.</td></tr>
<tr><td style="word-wrap: break-word;">system_prompt</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Detailed instructions defining the agent’s personality, knowledge domain, and behavioral guidelines.</td><td style="word-wrap: break-word;">You are a careful, citation-focused assistant. Provide concise answers and include source links.</td></tr>
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
<tr><td style="word-wrap: break-word;">agent</td><td style="word-wrap: break-word;">AGENT</td><td style="word-wrap: break-word;">An agent configuration object containing the chosen model, enabled tools, role, and system prompt. Intended for downstream execution/orchestration nodes.</td><td style="word-wrap: break-word;">{"model": "gpt-4o", "tools": {"Calculator": true, "WebSearcher": true}, "role": "A research assistant.", "system_prompt": "You are precise and cite sources."}</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Model and tool lists**: The available models and tools are fetched from the Agents service at runtime; if the service is unreachable, a built-in fallback list is used.
- **Output is configuration only**: This node does not perform inference or make network calls during execution; it just assembles an agent config.
- **Tools default state**: By default, all tools are disabled. Explicitly enable only the tools the agent needs.
- **Role usage**: The role is prepended to the system prompt and may be used by agent teams to decide which agent should speak next or take on a task.

## Troubleshooting
- **Missing or unexpected models/tools**: If you don’t see expected options, the service may be unreachable and a fallback list is being used. Check connectivity to the Agents service and refresh.
- **Downstream node rejects agent**: Ensure the tools you enabled are supported by the execution/orchestration node and that required fields (model, role, system_prompt) are set.
- **Agent behavior is off**: Refine the role and system prompt. The role should succinctly define the agent’s purpose; the system prompt should include explicit behavioral rules.
