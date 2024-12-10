# LLM Nodes

LLM (Large Language Model) nodes allow you to interact with various AI models directly in your workflows. Each LLM provider (Anthropic, OpenAI, Google, etc.) has its own node with consistent interfaces but unique model options.

<img src="/images/nodes/llms.png" alt="LLM Nodes">

## Required Inputs

| Input Field | Description | Configuration Details |
|------------|-------------|----------------------|
| Model | Select which model version to use | • Options vary by provider<br>• More powerful models for analysis/creation<br>• Basic models for simple tasks<br>• Example: Claude 3.5 Sonnet (Anthropic) |
| System Prompt | Instructions that control how the AI responds | • Multiline text field<br>• Sets formatting, style, and tone<br>• Default: "Respond directly to the user's message. Be concise." |
| Prompt | Main input field for your request | • Supports dynamic input references<br>• Press "/" to access available inputs<br>• Can reference knowledge base content |
| Temperature | Controls response creativity | • Range: 0.0 to 1.0 (slider)<br>• Default: 0.5<br>• Lower = more focused<br>• Higher = more creative |
| Max Tokens | Controls maximum response length | • Range: 0 to 4096 (slider)<br>• Default: 1024 (~750 words)<br>• Adjust based on needed length |

## Optional Context Inputs

| Input | Purpose | Use Cases |
|-------|----------|-----------|
| Input 1 | Primary context slot | • Main knowledge base<br>• Primary reference material |
| Input 2 | Secondary context slot | • Supporting information<br>• Additional context |
| Input 3 | Tertiary context slot | • Supplementary data<br>• Extra references |
| Input 4 | Quaternary context slot | • Final context layer<br>• Additional materials |

### Context Input Features
- Connect to knowledge bases
- Pass in reference materials
- Include additional context
- Chain outputs from other nodes

## Dynamic Input System

### Input Reference Syntax

| Step | Action | Details |
|------|--------|----------|
| 1 | Trigger Menu | Type "/" in prompt field |
| 2 | Select Input | Choose from input_1 through input_4 |
| 3 | Reference Format | Appears as {{input_1}} in prompt |
| 4 | Runtime Behavior | References replaced with actual values |

### Example Prompt
```
Context: {{input_1}}
Based on the above context, please summarize the main points.
```

## Output Specifications

| Output | Type | Description | Usage |
|--------|------|-------------|--------|
| Output | STRING | AI model's response | • Contains generated text<br>• Format based on prompts<br>• Can connect to other nodes |

## Best Practices

### Model Selection
| Consideration | Recommendation |
|---------------|----------------|
| Complex Tasks | Use stronger models |
| Simple Tasks | Choose basic models |
| Cost Efficiency | Consider performance tradeoffs |

### System Prompts
| Aspect | Guidance |
|--------|-----------|
| Format | Be specific about desired output |
| Tone | Set clear expectations |
| Rules | Include consistent instructions |

### Context Management
| Practice | Implementation |
|----------|----------------|
| Knowledge Bases | Connect relevant sources |
| Input Priority | Order by importance |
| Dynamic References | Use appropriately |

### Response Configuration
| Setting | Guidelines |
|---------|------------|
| Max Tokens | Set based on needs |
| Context Limits | Consider model constraints |
| Buffer | Allow for longer responses |
