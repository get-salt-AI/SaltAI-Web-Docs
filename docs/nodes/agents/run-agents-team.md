# Run Agents Team

Orchestrates a team of AI agents to collaboratively solve a task using different coordination strategies.

<img src="/images/nodes/agents/run-agents-team.png" alt="Run Agents Team" class="rounded-lg">

## Quick Start

1. Create at least one agent using the Create Agent node.
2. Connect one or more agents to the Run Agents Team node.
3. Set the team type and provide a task description.
4. Run the node to get the conversation and summary.

## Setup Guide

### 1. Prepare Agents
1. Use the Create Agent node to configure each agent.
2. Connect the output of each agent to the corresponding agent input on this node.

### 2. Configure Team
1. Select the team type (Cycle, Smart, or Manage).
2. Enter the task for the agents to solve.
3. Adjust summarization and message/timeout limits as needed.

## Basic Usage

### Multi-Agent Collaboration
* Assign different roles or expertise to each agent.
* Use for brainstorming, problem-solving, or multi-step tasks.
* Choose the team type to control how agents interact.

## Configuration

### Required Inputs
| Field        | Description                                                        | Type    | Example                |
|--------------|--------------------------------------------------------------------|---------|------------------------|
| agent_1      | First agent in the team.                                           | AGENT   | agent_1                |
| team_type    | Coordination strategy: Cycle, Smart, or Manage.                    | STRING  | Cycle                  |
| task         | Task description for the agents to solve.                          | STRING  | "Summarize this text"  |
| summarize    | Whether to output a summary of the conversation.                   | BOOLEAN | True                   |
| max_messages | Maximum number of messages (0 = unlimited).                        | INT     | 10                     |
| max_timeout  | Maximum execution time in seconds.                                 | INT     | 300                    |

### Optional Inputs
| Field    | Description                                 | Type  | Example   |
|----------|---------------------------------------------|-------|-----------|
| agent_2  | Second agent in the team.                   | AGENT | agent_2   |
| agent_3  | Third agent in the team.                    | AGENT | agent_3   |
| ...      | Up to 31 agents can be added in total.      | AGENT | agent_31  |

### Outputs
| Field    | Description                                 | Example                |
|----------|---------------------------------------------|------------------------|
| messages | Full conversation between the agents.        | Markdown transcript    |
| summary  | Condensed summary of the agents' discussion. | "Key findings..."      |

## Best Practices

### Team Composition
* Assign clear, distinct roles to each agent for better collaboration.
* Limit the number of agents to what is necessary for the task.

### Task Design
* Provide a clear, specific task description.
* Use summarization for concise outputs when needed.

## Troubleshooting

### Common Issues
* **Agents do not respond:** Ensure each agent is properly configured and connected.
* **Timeouts occur:** Increase the max_timeout value for longer tasks.
* **No summary output:** Check that the summarize option is enabled.

### Need Help?
* Review agent configurations and input values.
* Check logs for error messages.
* Consult platform documentation for further guidance.
