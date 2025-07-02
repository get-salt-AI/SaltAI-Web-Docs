# Tavily Web Search

Tavily Web Search enables automated, configurable web searches with options for depth, topic, and result filtering, returning both concise answers and relevant documents.

<img src="/images/nodes/agents/tavily-web-search.png" alt="Tavily Web Search" class="rounded-lg">

## Quick Start

1. Add the Tavily Web Search node to your workflow.
2. Enter your search query and adjust search parameters as needed.
3. Connect outputs to downstream nodes to use the answer or documents.

## Setup Guide

### 1. Node Placement
1. Locate the Tavily Web Search node in the Agents category.
2. Drag it into your workspace.

### 2. Configuration
1. Fill in the required search query.
2. Adjust search depth, topic, and other parameters as needed.
3. Connect outputs to desired nodes.

## Basic Usage

### General Web Search
* Retrieve concise answers and supporting documents for a given query.
* Filter results by topic (general/news) and domains.

### News Search
* Set topic to "news" and specify the number of days to search recent news articles.

## Configuration

### Required Inputs
| Field              | Description                                                                 | Type     | Example         |
|--------------------|-----------------------------------------------------------------------------|----------|-----------------|
| query              | The search query string.                                                    | STRING   | AI in medicine  |
| search_depth       | Depth of the search: basic (1cr) or advanced (2cr).                         | STRING   | basic           |
| max_results        | Maximum number of search results to return.                                 | INT      | 5               |
| topic              | Category of the search: general or news.                                    | STRING   | news            |
| days               | (News only) Number of days back to include in results.                      | INT      | 3               |
| include_answer     | Include a short answer to the original query.                               | BOOLEAN  | True            |
| include_raw_content| Include raw text content of search results.                                 | BOOLEAN  | False           |
| include_domains    | Domains to specifically include (one per line).                             | STRING   | example.com     |
| exclude_domains    | Domains to specifically exclude (one per line).                             | STRING   | spam.com        |

### Optional Inputs
*None*

### Outputs
| Field    | Description                                 | Example         |
|----------|---------------------------------------------|-----------------|
| answer   | Short answer to the query (if enabled).     | "AI is..."      |
| documents| Relevant search result documents (JSON).    | "[{...}]"       |

## Best Practices

### Query Construction
* Use clear, specific queries for best results.
* Leverage domain filters to narrow or broaden search scope.

### Resource Management
* Use "basic" search depth for quick, low-cost queries.
* Limit max_results to avoid excessive output and cost.

## Troubleshooting

### Common Issues
* **No results returned**: Check query spelling and try broadening the topic or increasing max_results.
* **Unexpected content**: Adjust include/exclude domains or enable raw content for more context.

### Need Help?
* Refer to platform documentation for node usage examples.
* Verify your network connection if searches consistently fail.
