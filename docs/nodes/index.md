# Nodes
!!! note
    We are actively working on documenting all of our nodes. Check back soon for more details!

Nodes are the fundamental building blocks of Salt workflows. Each node is a specialized component that performs a specific function, from processing input data to generating AI content to writing outputs. By connecting nodes together, you can create sophisticated AI workflows without writing code.



## Understanding Nodes

Every node in Salt follows a consistent pattern:
- **Inputs**: Data or parameters that the node needs to perform its function
- **Processing**: The core functionality that transforms or generates data
- **Outputs**: The results that can be passed to other nodes

## Node Categories

### Inputs [3fd401d6]
Input nodes serve as the entry points for data in your workflows. They handle various types of inputs including:
- Text input
- File uploads
- API data
- Triggers for workflow automation

#### Triggers [2ebe5daf]
Specialized input nodes that initiate workflows based on specific events or conditions.

### Large Language Models (LLMs) [a5851072]
Nodes that interface with various LLM providers to perform text generation and analysis:
- OpenAI LLM
- Anthropic LLM
- Gemini LLM
- Groq LLM

### Diffusion [b86b1fa4]
Image generation and manipulation nodes powered by diffusion models:

#### Stability [473f31da]
Advanced image generation capabilities including:
- Text-to-image generation
- Image editing and manipulation
- Style transfer
- Upscaling

### Knowledge Bases [44c6578c]
Nodes that connect to various data sources and knowledge management systems:
- Notion integration
- Slack connectivity
- Google Workspace integration
- Enterprise system connections (Salesforce, ServiceNow)
- Cloud storage (S3, Google Cloud Storage, Azure)

### Utilities [a04291ac]
Helper nodes that provide essential workflow functionality:
- Data inspection and debugging
- Format conversion
- Flow control
- Data transformation

### Outputs [11910f90]
Nodes that handle the final results of your workflows:

#### Writers [64c6157e]
Specialized output nodes for different destinations:
- File writers
- API endpoints
- External service integrations
- Database connections

### Agents [fe8e4eb1]
Autonomous components that can perform complex tasks:
- Web research
- Content analysis
- Data processing
- Task automation

## Best Practices

When working with nodes:

1. **Start Simple**
   - Begin with basic workflows using a few nodes
   - Test connections between nodes before adding complexity
   - Use the Inspect node to debug data flow

2. **Plan Your Data Flow**
   - Consider the type of data each node expects
   - Ensure compatibility between connected nodes
   - Plan for error handling

3. **Optimize for Performance**
   - Use appropriate node types for your task
   - Consider resource usage of heavy processing nodes
   - Batch operations when possible

## Getting Started

To begin building with nodes:

1. Browse the available nodes in each category
2. Experiment with simple connections
3. Use the documentation for each node to understand its capabilities
4. Start with template workflows and modify them for your needs

## Need Help?

Each node has detailed documentation about its:
- Required inputs
- Available options
- Output formats
- Common use cases
- Example configurations

Browse the specific node categories above to learn more about each type of node and its capabilities.
