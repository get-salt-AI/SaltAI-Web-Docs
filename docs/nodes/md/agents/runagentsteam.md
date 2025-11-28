# Run Agents Team

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Runs a multi-agent conversation to solve a given task using 1–31 agents. You choose the team organization mode (Cycle, Smart, or Manage), optionally enable summarization, and control limits like maximum messages and execution time. The node returns the chat transcript (as a markdown-formatted string) and an optional concise summary.

</div>
<div style="flex: 0 0 300px;"><img src="../../../images/previews/agents/runagentsteam.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you want multiple agents (created via Create Agent) to collaborate on a task. Connect agent_1 (required) and optionally chain agent_2 through agent_31 in order. Select a team type: Cycle (round-robin), Smart (LLM chooses next speaker), or Manage (conversation managed by an orchestrator). Provide the task, optionally enable summarization, and set limits for message count and timeout. The output includes a formatted transcript and a summary to feed into downstream steps.

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
<tr><td style="word-wrap: break-word;">agent_1</td><td>True</td><td style="word-wrap: break-word;">AGENT</td><td style="word-wrap: break-word;">Primary agent configuration and capabilities. This is the first team member.</td><td style="word-wrap: break-word;">{'model': 'gpt-4o', 'tools': {'WebSearcher': True, 'Calculator': False}, 'role': 'Research assistant', 'system_prompt': 'You help with web research and synthesis.'}</td></tr>
<tr><td style="word-wrap: break-word;">team_type</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">How the team coordinates: Cycle (round-robin), Smart (LLM picks next speaker), or Manage (orchestrator manages flow).</td><td style="word-wrap: break-word;">Smart</td></tr>
<tr><td style="word-wrap: break-word;">task</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The task or problem statement the agents should collaboratively solve.</td><td style="word-wrap: break-word;">Compare the latest AI model benchmarks and recommend the best for document summarization.</td></tr>
<tr><td style="word-wrap: break-word;">summarize</td><td>True</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">If true, returns a concise summary of the multi-agent discussion focused on solving the task.</td><td style="word-wrap: break-word;">True</td></tr>
<tr><td style="word-wrap: break-word;">max_messages</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Maximum number of messages the team can produce. 0 means no limit.</td><td style="word-wrap: break-word;">20</td></tr>
<tr><td style="word-wrap: break-word;">max_timeout</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Maximum execution duration in seconds for the agents team run.</td><td style="word-wrap: break-word;">300</td></tr>
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
<tr><td style="word-wrap: break-word;">messages</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The multi-agent conversation transcript as a markdown-formatted string containing all messages.</td><td style="word-wrap: break-word;">```markdown Agent_1: Proposal A looks promising.  Agent_2: I’ll verify with recent data. ```</td></tr>
<tr><td style="word-wrap: break-word;">summary</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">A concise summary of the conversation outcome. May be empty if summarization is disabled.</td><td style="word-wrap: break-word;">Recommendation: Use Model X for document summarization due to superior accuracy and latency.</td></tr>
</tbody>
</table>
</div>

## Important Notes
- Agents must be connected sequentially: each agent_n becomes available only after agent_(n-1) is set.
- You can run with a single agent by only providing agent_1.
- Team types: Cycle (round-robin), Smart (next speaker selected via an LLM), Manage (conversation orchestrated).
- max_messages = 0 removes the cap on the number of produced messages.
- The transcript output is wrapped as a markdown-formatted string.
- Set max_timeout high enough for complex tasks to avoid premature termination.
- If summarization is disabled, the summary output may be empty or minimal depending on the task.

## Troubleshooting
- Request failed or timed out: Increase max_timeout and try again; ensure the task is appropriately scoped.
- Non-200 response from the service: Review the error message and adjust inputs (e.g., reduce complexity, change team_type).
- JSON decoding error: Retry the run; if persistent, simplify the task or reduce max_messages.
- Empty or unhelpful summary: Enable summarize or refine agent roles/prompts to produce clearer outcomes.
- Conversation stops early: Increase max_messages or verify that added agents are properly configured and connected in order.

## Example Pipelines

<figure style="margin: 20px 0;">
  <img src="../../../images/assets/agents/runagentsteam/example.png" alt="Example" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" />
  <figcaption style="margin-top: 8px; text-align: center; font-style: italic; color: #666;">Example</figcaption>
</figure>

