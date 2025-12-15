# Run Agents Team

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Coordinates a team of one or more AI agents to collaborate on a task. You can choose how the conversation is organized (Cycle, Smart, or Manage), limit message count and runtime, and optionally request a final summary. Returns the multi-agent conversation transcript and a condensed summary.

</div>
<div style="flex: 0 0 300px;"><img src="../../../images/previews/agents/runagentsteam.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node after creating one or more agents with Create Agent. Connect the agents to agent_1 (and agent_2 ... agent_31 as needed), provide the task, select the team interaction type, and set constraints (message limit and timeout). Useful for complex tasks that benefit from multiple specialized agents discussing and solving a problem.

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
<tr><td style="word-wrap: break-word;">agent_1</td><td>True</td><td style="word-wrap: break-word;">AGENT</td><td style="word-wrap: break-word;">First agent (and minimum required). Defines the initial team member. You can run with only one agent or add more using agent_2 ... agent_31.</td><td style="word-wrap: break-word;">AGENT object from Create Agent</td></tr>
<tr><td style="word-wrap: break-word;">team_type</td><td>True</td><td style="word-wrap: break-word;">['Cycle', 'Smart', 'Manage']</td><td style="word-wrap: break-word;">Interaction mode for the team: Cycle (round-robin speaking), Smart (next speaker chosen by an LLM based on context), or Manage (conversation flow managed by an orchestrator).</td><td style="word-wrap: break-word;">Smart</td></tr>
<tr><td style="word-wrap: break-word;">task</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The task or problem statement the agents should solve together.</td><td style="word-wrap: break-word;">Draft a product launch plan for a new mobile app targeting college students.</td></tr>
<tr><td style="word-wrap: break-word;">summarize</td><td>True</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">If true, returns a concise summary of the agents' discussion and final answer in addition to the transcript.</td><td style="word-wrap: break-word;">true</td></tr>
<tr><td style="word-wrap: break-word;">max_messages</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Maximum number of messages the team may generate. 0 means no limit.</td><td style="word-wrap: break-word;">20</td></tr>
<tr><td style="word-wrap: break-word;">max_timeout</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Maximum execution time in seconds. If exceeded, the run is aborted.</td><td style="word-wrap: break-word;">300</td></tr>
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
<tr><td style="word-wrap: break-word;">messages</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Full transcript of the multi-agent conversation as a single formatted string.</td><td style="word-wrap: break-word;">Agent_1: Outlines approach...  Agent_2: Adds research...  Agent_1: Proposes final plan...</td></tr>
<tr><td style="word-wrap: break-word;">summary</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Concise summary of the conversation and answer to the task. May be empty if summarization is disabled.</td><td style="word-wrap: break-word;">A three-phase launch strategy with timeline, channels, and KPIs.</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Team organization**: Cycle is round-robin; Smart selects the next speaker contextually; Manage uses an orchestrator to guide the flow.
- **Agent chaining**: Additional agent inputs (agent_2 to agent_31) appear only after the previous agent input is connected.
- **Single-agent mode**: You can run with only agent_1 connected if you don't need a multi-agent discussion.
- **Message limit**: Set max_messages to 0 for no limit; otherwise, the team stops when the limit is reached.
- **Timeouts**: max_timeout bounds the total execution time; ensure it’s large enough for your task complexity.
- **Transcript and summary**: The transcript includes the full dialog; the summary is a condensed answer and may be empty if summarize is false.
- **Agent configuration**: Agents inherit their model, tools, role, and system prompt from the AGENT objects you connect (created via Create Agent).
- **Capacity**: Up to 31 agents are supported (agent_1 through agent_31).

## Troubleshooting
- **Empty or missing summary**: Ensure summarize is set to true; otherwise the summary may be empty.
- **Run stops early**: Increase max_messages if the conversation reaches the message cap; verify that team_type suits your needs.
- **Timeouts or failures**: Increase max_timeout for longer tasks. Network or service issues may cause failures; retry after adjusting timeout.
- **No optional agent inputs visible**: Connect agent_1 first; subsequent agent inputs appear only after the previous is set.
- **Unexpected conversation quality**: Review each agent’s role and system prompt from Create Agent, and consider switching team_type (e.g., Smart instead of Cycle).

## Example Pipelines

<figure style="margin: 20px 0;">
  <img src="../../../images/assets/agents/runagentsteam/example.png" alt="Example" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" />
  <figcaption style="margin-top: 8px; text-align: center; font-style: italic; color: #666;">Example</figcaption>
</figure>

