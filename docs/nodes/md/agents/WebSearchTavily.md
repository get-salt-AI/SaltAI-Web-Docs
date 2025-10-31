# Tavily Web Search

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Performs a live web search using the Tavily service and returns a concise answer (optional) along with structured search results. Supports general and news topics, configurable search depth, result limits, and domain inclusion/exclusion. Can optionally include raw page content or AI-extracted relevant snippets.

</div>
<div style="flex: 0 0 300px;"><img src="../../../images/previews/agents/WebSearchTavily.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node to research a topic, gather citations, and optionally get a short synthesized answer. Typical workflows include: triggering a search from a user query, feeding the returned documents into downstream summarizers or RAG pipelines, and using the optional answer as a quick response while detailed processing uses the documents list.

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
<tr><td style="word-wrap: break-word;">query</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The search query text to send to the Tavily engine.</td><td style="word-wrap: break-word;">What are the latest developments in quantum error correction?</td></tr>
<tr><td style="word-wrap: break-word;">search_depth</td><td>True</td><td style="word-wrap: break-word;">['basic', 'advanced']</td><td style="word-wrap: break-word;">Controls how deep the search goes. Basic is faster and cheaper; Advanced performs broader/deeper retrieval.</td><td style="word-wrap: break-word;">advanced</td></tr>
<tr><td style="word-wrap: break-word;">max_results</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Maximum number of results to return. Minimum is 1.</td><td style="word-wrap: break-word;">5</td></tr>
<tr><td style="word-wrap: break-word;">topic</td><td>True</td><td style="word-wrap: break-word;">['general', 'news']</td><td style="word-wrap: break-word;">Select the search category. Use 'news' for time-sensitive results.</td><td style="word-wrap: break-word;">news</td></tr>
<tr><td style="word-wrap: break-word;">days</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Only applies when topic is 'news'. Number of days back from today to include in results.</td><td style="word-wrap: break-word;">3</td></tr>
<tr><td style="word-wrap: break-word;">include_answer</td><td>True</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">If true, returns a short synthesized answer to the query along with documents.</td><td style="word-wrap: break-word;">True</td></tr>
<tr><td style="word-wrap: break-word;">include_raw_content</td><td>True</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">If true, includes raw text content from the pages. If false, returns AI-extracted, query-relevant snippets only.</td><td style="word-wrap: break-word;">False</td></tr>
<tr><td style="word-wrap: break-word;">include_domains</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">List of domains to prioritize/include, separated by line breaks.</td><td style="word-wrap: break-word;">nature.com arxiv.org aeon.co</td></tr>
<tr><td style="word-wrap: break-word;">exclude_domains</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">List of domains to exclude, separated by line breaks.</td><td style="word-wrap: break-word;">reddit.com pinterest.com quora.com</td></tr>
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
<tr><td style="word-wrap: break-word;">answer</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">A short, synthesized answer to the original query. Present only if include_answer is true.</td><td style="word-wrap: break-word;">Quantum error correction is progressing with bosonic codes and LDPC-based surface code variants showing improved thresholds and resource efficiency.</td></tr>
<tr><td style="word-wrap: break-word;">documents</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">A JSON-formatted string representing an array of search results with titles, URLs, snippets, and optionally raw content.</td><td style="word-wrap: break-word;">[{"title": "Recent Advances in QEC", "url": "https://example.com/qec", "snippet": "...", "content": "..."}]</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **All inputs are required**: Provide values for each field, even if left as defaults.
- **Query must be non-empty**: An empty query will cause the node to fail with a validation error.
- **Answer output depends on include_answer**: If set to false, the 'answer' output may be empty or omitted by the service; rely on documents for downstream processing.
- **Days applies only to news topic**: The 'days' parameter is used only when topic is 'news'; it is ignored for 'general'.
- **Domain lists are line-separated**: Provide one domain per line for include_domains and exclude_domains.
- **Raw content can be large**: Enabling include_raw_content may significantly increase payload size and processing time.
- **Search depth affects cost and coverage**: Advanced depth provides broader retrieval but at higher credit usage.

## Troubleshooting
- **Empty query error**: Ensure 'query' is a non-blank string; trim whitespace before passing.
- **No answer returned**: Set include_answer to true; if already true, the service may still omit an answer depending on query and content.
- **Cannot parse documents**: The documents output is a JSON string; parse it before iterating over results in downstream nodes or scripts.
- **Unexpected results for 'general' with 'days'**: 'days' is ignored unless topic is 'news'; switch topic to 'news' to filter by recency.
- **Too few or too many results**: Adjust max_results to your desired count; minimum is 1.
- **Irrelevant domains appearing**: Verify exclude_domains formatting (one domain per line, no protocols). For stricter control, also use include_domains.
- **Timeouts or service errors**: Retry the operation later and confirm network connectivity; reduce include_raw_content and max_results to decrease response size.

## Example Pipelines

<figure style="margin: 20px 0;">
  <img src="../../../images/assets/agents/WebSearchTavily/Example.png" alt="Example" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" />
  <figcaption style="margin-top: 8px; text-align: center; font-style: italic; color: #666;">Example</figcaption>
</figure>

