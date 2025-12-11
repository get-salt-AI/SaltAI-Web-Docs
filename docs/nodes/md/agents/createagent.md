# Create Agent

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Builds a reusable agent configuration object that defines the model, allowed tools, role, and system prompt for an AI agent. This node does not execute the agent; it prepares the agent profile to be connected to downstream nodes (e.g., multi-agent orchestration).

</div>
<div style="flex: 0 0 300px;"><img src="../../../images/previews/agents/createagent.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node at the start of an agents workflow to define one or more agents. Configure the target LLM model, select which external tools the agent may use, and specify the role and system prompt. Connect the resulting AGENT output to nodes that run agents or teams of agents.

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
<tr><td style="word-wrap: break-word;">model</td><td>True</td><td style="word-wrap: break-word;">CHOICE</td><td style="word-wrap: break-word;">Select the LLM model the agent will use for inference. Options are provided by the platform and may include popular model families.</td><td style="word-wrap: break-word;">gpt-4o</td></tr>
<tr><td style="word-wrap: break-word;">tools</td><td>True</td><td style="word-wrap: break-word;">MULTICOMBO</td><td style="word-wrap: break-word;">Choose external tools the agent is permitted to use. Each selected tool expands the agent's capabilities (e.g., browsing, scraping, calculators, data extraction).</td><td style="word-wrap: break-word;">{'Calculator': True, 'DateTime': True, 'WebSearcher': False, 'WebScraper': False, 'JSONExtractor': False, 'CSVExtractor': False}</td></tr>
<tr><td style="word-wrap: break-word;">role</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">A short, high-level description of the agent's role and expertise. Used to help select or route the agent in team settings and is prepended to the system prompt.</td><td style="word-wrap: break-word;">A helpful AI assistant specialized in data analysis and reporting.</td></tr>
<tr><td style="word-wrap: break-word;">system_prompt</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The detailed system prompt defining the agent's persona, domain knowledge, and behavioral guidelines.</td><td style="word-wrap: break-word;">You are a meticulous data analyst. Always explain your methodology and cite assumptions. Prefer concise, structured answers.</td></tr>
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
<tr><td style="word-wrap: break-word;">agent</td><td style="word-wrap: break-word;">AGENT</td><td style="word-wrap: break-word;">An agent configuration object encapsulating the selected model, enabled tools, role, and system prompt. Intended for connection to nodes that run agents or agent teams.</td><td style="word-wrap: break-word;">{'model': 'gpt-4o', 'tools': {'Calculator': True, 'DateTime': True, 'WebSearcher': False, 'WebScraper': False, 'JSONExtractor': False, 'CSVExtractor': False}, 'role': 'A helpful AI assistant specialized in data analysis and reporting.', 'system_prompt': 'You are a meticulous data analyst. Always explain your methodology and cite assumptions. Prefer concise, structured answers.'}</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **This node creates configuration only**: It does not run any inference. Connect its AGENT output to execution nodes (e.g., a team runner) to produce results.
- **Role is prepended to the system prompt**: Keep the role concise and use the system prompt for detailed behavior and constraints.
- **Tools control capabilities**: If no tools are selected, the agent operates as a pure LLM without external actions.
- **Model availability**: The list of selectable models and tools is provided by the platform and may change over time.

## Troubleshooting
- **No or unexpected model options**: If the model list looks limited or outdated, refresh the interface or check platform settings; select an available model closest to your needs.
- **Agent lacks expected capabilities**: Verify the required tools are enabled in the tools selection. Some actions require specific tools (e.g., web browsing, data extraction).
- **Downstream node errors**: Ensure the AGENT output is connected to a compatible node that expects an AGENT input.
- **Behavior not matching expectations**: Refine the system prompt for more explicit instructions and adjust the role to better signal the agent's specialization.
