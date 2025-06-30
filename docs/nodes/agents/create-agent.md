# Create Agent

Creates a configurable AI agent with a specified model, tools, role, and system prompt for use in multi-agent workflows.

<img src="/images/nodes/agents/create-agent.png" alt="Create Agent" class="rounded-lg">

## Quick Start

1. Select the desired LLM model from the dropdown.
2. Choose one or more external tools for the agent to access.
3. Define the agent's role and system prompt.
4. Connect the output to downstream nodes requiring an agent.

## Setup Guide

### 1. Model and Tools Selection
1. Open the node configuration panel.
2. Pick an LLM model suitable for your use case.
3. Select external tools the agent should use (e.g., Calculator, WebScraper).

### 2. Define Agent Role and Prompt
1. Enter a concise role description to guide agent selection in teams.
2. Write a detailed system prompt to shape the agent's behavior and knowledge domain.

## Basic Usage

### Single Agent Creation
* Use to instantiate a single AI agent for downstream tasks.
* Combine with team or orchestration nodes for multi-agent scenarios.

## Configuration

### Required Inputs
| Field         | Description                                                      | Type     | Example                |
|--------------|------------------------------------------------------------------|----------|------------------------|
| model        | LLM model to use for agent inference                             | String   | gpt-4o                 |
| tools        | External tools available to the agent                            | Multi    | Calculator, WebScraper |
| role         | Short description of the agent's role                            | String   | Data analyst           |
| system_prompt| System prompt defining agent's personality and guidelines         | String   | You are a helpful AI assistant. |

### Optional Inputs
*None*

### Outputs
| Field | Description                | Example |
|-------|----------------------------|---------|
| agent | Configured agent object    | —       |

## Best Practices

### Agent Design
* Clearly define the agent's role and system prompt for predictable behavior.
* Select only the tools necessary for the agent's tasks to minimize risk.

### Model Selection
* Choose the most capable model available for complex reasoning tasks.
* Use lighter models for cost-sensitive or high-throughput scenarios.

## Troubleshooting

### Common Issues
* **Agent does not behave as expected**: Refine the system prompt and role description for clarity.
* **Model or tool not available**: Ensure backend services are running and accessible.

### Need Help?
* Consult platform documentation for supported models and tools.
* Check logs for error messages if agent creation fails.
