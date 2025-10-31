# Run Agents Team

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Runs a coordinated multi-agent conversation to solve a given task. You can add 1–31 agents and choose how the discussion proceeds (round-robin, LLM-selected next speaker, or orchestrator-managed). Returns the full conversation transcript and an optional concise summary.

</div>
<div style="flex: 0 0 300px;"><img src="../../../images/previews/agents/RunAgentsTeam.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node to orchestrate collaboration among multiple specialized agents created with Create Agent. Connect one or more agent configs, define the task, choose the team type, and optionally cap length and duration. Ideal for complex problems that benefit from debate, tool-usage, or role specialization, with a final summarized answer if desired.

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
<tr><td style="word-wrap: break-word;">agent_1</td><td>True</td><td style="word-wrap: break-word;">AGENT</td><td style="word-wrap: break-word;">First team member configuration. Produced by Create Agent.</td><td style="word-wrap: break-word;">AGENT object from Create Agent (e.g., GPT-4o with WebSearcher tool and a Researcher role)</td></tr>
<tr><td style="word-wrap: break-word;">team_type</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Defines the discussion flow: Cycle (round-robin turns), Smart (LLM selects next speaker based on context), Manage (an orchestrator manages who speaks when).</td><td style="word-wrap: break-word;">Cycle</td></tr>
<tr><td style="word-wrap: break-word;">task</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The problem or objective for the agents to solve. Provide clear instructions and constraints.</td><td style="word-wrap: break-word;">Research the top three note-taking strategies for software engineers and propose a concise, actionable plan.</td></tr>
<tr><td style="word-wrap: break-word;">summarize</td><td>True</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">If true, the node will also produce a concise summary/answer to the task based on the conversation.</td><td style="word-wrap: break-word;">true</td></tr>
<tr><td style="word-wrap: break-word;">max_messages</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Maximum number of messages produced by the team. Use 0 for no limit.</td><td style="word-wrap: break-word;">12</td></tr>
<tr><td style="word-wrap: break-word;">max_timeout</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Maximum execution time in seconds for the team run.</td><td style="word-wrap: break-word;">300</td></tr>
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
<tr><td style="word-wrap: break-word;">messages</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The complete transcript of the agents' conversation, formatted as markdown.</td><td style="word-wrap: break-word;">A multi-turn, markdown-formatted dialogue between the agents discussing and solving the task.</td></tr>
<tr><td style="word-wrap: break-word;">summary</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">A concise summary or final answer distilled from the conversation (may be empty or brief if summarization is disabled or not applicable).</td><td style="word-wrap: break-word;">Top three strategies summarized with a short action plan.</td></tr>
</tbody>
</table>
</div>

## Important Notes
- Agents must be created with Create Agent first and connected here as agent_1, agent_2, etc.
- You can run with a single agent if desired; additional agents are optional and appear incrementally as you connect them.
- team_type determines how turns are selected: Cycle (fixed order), Smart (context-aware selection), Manage (orchestrator-controlled).
- Set max_messages to 0 to allow unlimited conversation length within max_timeout.
- If summarize is disabled, the summary output may be empty or minimal.

## Troubleshooting
- Conversation stops too early: increase max_messages or provide a clearer task to guide the discussion.
- Run times out: increase max_timeout or reduce the number of agents/complexity of the task.
- Summary is missing or too brief: ensure summarize is enabled and that the task clearly asks for actionable conclusions.
- Agents seem off-topic: refine each agent's role and system prompt in Create Agent, and provide a more specific task.

## Example Pipelines

<figure style="margin: 20px 0;">
  <img src="../../../images/assets/agents/RunAgentsTeam/Example.png" alt="Example" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" />
  <figcaption style="margin-top: 8px; text-align: center; font-style: italic; color: #666;">Example</figcaption>
</figure>

