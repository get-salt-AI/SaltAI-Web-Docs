# Google Web Search

Performs a Google web search and returns relevant documents based on your query, supporting advanced search notations and filters.

<img src="/images/nodes/agents/google-web-search.png" alt="Google Web Search" class="rounded-lg">

## Quick Start

1. Add the Google Web Search node to your workflow.
2. Enter your search query (optionally using advanced notations).
3. Configure result limits and filters as needed.
4. Run the node to retrieve search results.

## Setup Guide

### 1. Add Node to Workflow
1. Locate the Google Web Search node in the node library.
2. Drag it into your workspace.

### 2. Configure Search Parameters
1. Enter your search query in the provided field.
2. Adjust optional parameters such as result count, SafeSearch, and time limits.

### 3. Execute Search
1. Connect the node as needed and run your workflow.
2. Review the returned documents.

## Basic Usage

### General Web Search
* Retrieve top web results for a given query.
* Use advanced notations for precise searches (e.g., site restriction, file type).

### Filtered Search
* Limit results by time (e.g., last week, month).
* Enable SafeSearch to filter explicit content.

## Configuration

### Required Inputs
| Field        | Description                                              | Type     | Example                |
|-------------|----------------------------------------------------------|----------|------------------------|
| query       | The search query. Supports Google search notations.      | STRING   | "AI research site:arxiv.org" |
| max_results | Maximum number of results to return (1-10).              | INT      | 5                      |
| safesearch  | Enable SafeSearch filtering.                            | BOOLEAN  | False                  |
| timelimit   | Timeframe for results: None, d, w, m, y.                | STRING   | "w"                    |
| timelimit_number | Number of time units for the time limit.            | INT      | 2                      |

### Optional Inputs
*None*

### Outputs
| Field     | Description                        | Example                |
|-----------|------------------------------------|------------------------|
| documents | JSON string of search results.     | '[{"title": ..., ...}]' |

## Best Practices

### Query Construction
* Use advanced notations (quotes, minus, site:, filetype:) for targeted results.
* Combine filters for more relevant output.

### Result Management
* Limit max_results to avoid excessive data.
* Use timelimit to focus on recent information.

## Troubleshooting

### Common Issues
* **No results returned**: Check your query for typos or overly restrictive filters.
* **Unexpected content**: Adjust SafeSearch or time filters for better relevance.

### Need Help?
* Refer to platform documentation for workflow integration tips.
* For persistent issues, consult your system administrator.
