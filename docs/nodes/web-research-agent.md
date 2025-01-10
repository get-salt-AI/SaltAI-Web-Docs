# Web Research Agent

Conduct automated web research and generate structured reports. Uses multiple LLM models to search, analyze, and synthesize information from the web, specific URLs, or local documents.

This node is powered by [GPT Researcher](https://github.com/assafelovic/gpt-researcher), an open-source autonomous research agent that conducts comprehensive online research on any given topic.

<img src="/images/web-research-agent.png" alt="Web Research Agent Node" class="rounded-lg">

## Quick Start

1. Add Web Research Agent node to your workflow
2. Enter your search query
3. Select report type and format
4. Run to get a researched report with references

## Basic Usage

### Simple Web Search
* Enter your research query
* Choose "Summary" report type
* Select "Markdown" format
* Get a concise report with sources

### Custom Research
* Provide specific source URLs
* Add task context for better focus
* Select detailed report type
* Receive comprehensive analysis

## Configuration

### Required Inputs
| Field | Description | Type | Example |
|-------|-------------|------|---------|
| **query** | Your search query | String | `"Latest developments in AI safety"` |
| **report_type** | Type of report to generate | Select | `Summary`, `Analysis`, `Research` |
| **report_format** | Output format | Select | `Markdown`, `APA`, `Chicago` |
| **task_context** | Additional context | String | `"Focus on regulatory frameworks"` |

### Optional Inputs
| Field | Description | Type | Example |
|-------|-------------|------|---------|
| **source_urls** | Specific URLs to research | String | `"url1.com,url2.com"` |
| **local_documents_path** | Path to local documents | String | `"/path/to/docs"` |
| **combine_source_urls_with_web** | Include web search with URLs | Boolean | `true` |
| **combine_local_documents_with_web** | Include web search with local docs | Boolean | `false` |

### Model Settings
| Field | Description | Type | Default |
|-------|-------------|------|---------|
| **embed_model** | Model for context embedding | Select | `text-embedding-3-small` |
| **fast_llm_model** | Model for quick operations | Select | `gpt-4o-mini` |
| **smart_llm_model** | Model for detailed analysis | Select | `gpt-4o-2024-08-06` |
| **strategic_llm_model** | Model for planning | Select | `o1-preview` |

### Outputs
| Field | Description | Example |
|-------|-------------|---------|
| **report** | Generated research report | Markdown/formatted text |
| **references** | List of sources used | URL list with descriptions |

## Best Practices

### Query Formulation
* Be specific and clear in your query
* Include key terms and concepts
* Add context for better focus
* Specify time frames if relevant

### Source Management
* Combine different source types for comprehensive research
* Verify source URL formats
* Use local documents for proprietary information
* Balance between web search and specific sources

## Troubleshooting

### Common Issues
* **Empty Report**: Check query clarity and source availability
* **Timeout Error**: Reduce scope or split into multiple queries
* **Source Error**: Verify URL format and accessibility
* **Model Error**: Check model availability and permissions

### Need Help?
* Review source URL formatting
* Check local document path accessibility
* Verify model permissions
* Adjust query specificity
