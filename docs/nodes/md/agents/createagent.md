# Create Agent

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Builds an agent configuration by selecting a language model, enabling external tools, and defining the agent’s role and system prompt. It does not run any inference; it prepares a reusable AGENT object for downstream nodes. Model and tool options are dynamically fetched from the Agents service with sensible fallbacks.

</div>
<div style="flex: 0 0 300px;"><img src="../../../images/previews/agents/createagent.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node at the start of an agents workflow to define a single agent’s capabilities and behavior. Connect its AGENT output into nodes that orchestrate or run agents (e.g., a team runner) to execute tasks with the chosen model and tools.

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
<tr><td style="word-wrap: break-word;">model</td><td>True</td><td style="word-wrap: break-word;">SELECT</td><td style="word-wrap: break-word;">Select the LLM to power the agent. Options are fetched from the Agents service; if unavailable, a fallback list is used.</td><td style="word-wrap: break-word;">gpt-4o</td></tr>
<tr><td style="word-wrap: break-word;">tools</td><td>True</td><td style="word-wrap: break-word;">MULTICOMBO</td><td style="word-wrap: break-word;">Choose external tools the agent may call during reasoning. Each option can be toggled on/off.</td><td style="word-wrap: break-word;">{'Calculator': True, 'DateTime': False, 'WebScraper': False, 'WebSearcher': True, 'JSONExtractor': False, 'CSVExtractor': False}</td></tr>
<tr><td style="word-wrap: break-word;">role</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Short role description used to characterize the agent. This text is prepended to the system prompt and may be used when selecting agents in a team.</td><td style="word-wrap: break-word;">A helpful research assistant that finds and summarizes reputable sources.</td></tr>
<tr><td style="word-wrap: break-word;">system_prompt</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The agent’s core instructions: personality, scope, constraints, and behavior guidelines.</td><td style="word-wrap: break-word;">You are precise and cite sources. Prefer concise answers with bullet points when appropriate.</td></tr>
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
<tr><td style="word-wrap: break-word;">agent</td><td style="word-wrap: break-word;">AGENT</td><td style="word-wrap: break-word;">An agent configuration object containing the selected model, tools map, role, and system prompt. Use this as input to nodes that run or orchestrate agents.</td><td style="word-wrap: break-word;">{'model': 'gpt-4o', 'tools': {'Calculator': True, 'DateTime': False, 'WebScraper': False, 'WebSearcher': True, 'JSONExtractor': False, 'CSVExtractor': False}, 'role': 'A helpful research assistant that finds and summarizes reputable sources.', 'system_prompt': 'You are precise and cite sources. Prefer concise answers with bullet points when appropriate.'}</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Dynamic options**: Model and tool lists are fetched from the Agents service at runtime; if the service is unavailable, a fallback list is shown.
- **No execution**: This node only constructs an AGENT config; it does not generate messages or call the model.
- **Role usage**: The role is prepended to the system prompt and may influence agent selection when used in teams.
- **Tools control capabilities**: If no tools are selected, the agent relies solely on the LLM with no external capabilities (e.g., browsing, scraping, or parsing).
- **Compatibility**: The AGENT output is intended for use with agents orchestration nodes (e.g., team runners).

## Troubleshooting
- **Empty or missing model/tool options**: The Agents service may be unreachable. Retry, check network access, or use the fallback options presented.
- **Unexpected model unavailability at runtime**: Ensure the selected model is supported by your environment and service configuration.
- **Agent lacks expected capabilities**: Verify the corresponding tools are enabled in the tools multiselect.
- **Downstream node errors**: If a runner node fails using this agent, confirm the AGENT output is connected correctly and adjust any runtime limits (e.g., timeouts) in the runner node.
