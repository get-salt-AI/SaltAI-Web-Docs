# Tavily Web Search

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Performs a live web search using the Tavily search provider and returns a concise answer (optional) along with structured search results. The node calls the Agents service and outputs a JSON-formatted string of documents and, when requested, a short synthesized answer.

</div>
<div style="flex: 0 0 300px;"><img src="../../../images/previews/agents/websearchtavily.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need up-to-date information or sources from the web to ground an answer or collect references. Typical workflows: answer a user question and cite sources, gather recent news on a topic, or filter results to/from specific domains. Configure depth, topic, and domain filters to control the scope and recency of results.

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
<tr><td style="word-wrap: break-word;">query</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The search query text to send to Tavily.</td><td style="word-wrap: break-word;">What are the latest breakthroughs in quantum error correction?</td></tr>
<tr><td style="word-wrap: break-word;">search_depth</td><td>True</td><td style="word-wrap: break-word;">CHOICE</td><td style="word-wrap: break-word;">Depth of the search. Use 'basic' for a quicker, lighter search or 'advanced' for a deeper search.</td><td style="word-wrap: break-word;">advanced</td></tr>
<tr><td style="word-wrap: break-word;">max_results</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Maximum number of search results to return.</td><td style="word-wrap: break-word;">5</td></tr>
<tr><td style="word-wrap: break-word;">topic</td><td>True</td><td style="word-wrap: break-word;">CHOICE</td><td style="word-wrap: break-word;">Category of the search. Choose 'general' for broad results or 'news' to prioritize recent news.</td><td style="word-wrap: break-word;">news</td></tr>
<tr><td style="word-wrap: break-word;">days</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Only used when topic is 'news'. Number of days back from today to include in results.</td><td style="word-wrap: break-word;">3</td></tr>
<tr><td style="word-wrap: break-word;">include_answer</td><td>True</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">Whether to include a short synthesized answer to the query.</td><td style="word-wrap: break-word;">true</td></tr>
<tr><td style="word-wrap: break-word;">include_raw_content</td><td>True</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">If true, include raw text content from results; otherwise return only the most relevant extracted content.</td><td style="word-wrap: break-word;">false</td></tr>
<tr><td style="word-wrap: break-word;">include_domains</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Limit results to these domains. Provide one domain per line.</td><td style="word-wrap: break-word;">arxiv.org nature.com</td></tr>
<tr><td style="word-wrap: break-word;">exclude_domains</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Exclude results from these domains. Provide one domain per line.</td><td style="word-wrap: break-word;">wikipedia.org reddit.com</td></tr>
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
<tr><td style="word-wrap: break-word;">answer</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">A short, synthesized answer to the query if requested.</td><td style="word-wrap: break-word;">Quantum error correction progressed with bosonic codes enabling lower overhead fault tolerance; see sources for details.</td></tr>
<tr><td style="word-wrap: break-word;">documents</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">A JSON-formatted string containing the list of retrieved documents, including titles, URLs, snippets, and optionally raw content.</td><td style="word-wrap: break-word;">[{"title": "Paper on bosonic codes", "url": "https://arxiv.org/abs/xxxx.xxxxx", "snippet": "Recent advances in..."}]</td></tr>
</tbody>
</table>
</div>

## Important Notes
- Query must be a non-empty string; empty queries will cause an error.
- If 'include_answer' is disabled, the 'answer' output may be empty or omitted by the service; rely on 'documents' for sources.
- The 'days' parameter is applicable only when 'topic' is set to 'news'; otherwise it is ignored.
- Setting 'include_raw_content' to true can increase response size and processing time.
- Search depth affects the breadth/comprehensiveness of results; advanced depth may be slower or more resource-intensive.
- Domain filters expect one domain per line; avoid protocols (http/https) and paths.

## Troubleshooting
- Empty or whitespace-only 'query' causes an immediate validation error. Provide a meaningful query.
- If the node raises a request failure error, verify the Agents service is reachable and try again later.
- If the response parsing fails, reduce 'include_raw_content' or 'max_results' to lower payload size and retry.
- No answer returned: ensure 'include_answer' is set to true; otherwise use the 'documents' output to compose an answer.
- Unexpected or irrelevant results: refine 'query', switch 'search_depth' to 'advanced', or adjust domain include/exclude lists.
- Old news in results: set 'topic' to 'news' and tune 'days' to the desired recency window.

## Example Pipelines

<figure style="margin: 20px 0;">
  <img src="../../../images/assets/agents/websearchtavily/example.png" alt="Example" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" />
  <figcaption style="margin-top: 8px; text-align: center; font-style: italic; color: #666;">Example</figcaption>
</figure>

