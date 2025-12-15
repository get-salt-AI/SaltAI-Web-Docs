# Tavily Web Search

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Queries the Tavily web search service via Salt Agents and returns relevant results. Optionally includes a concise AI-generated answer alongside a JSON string of retrieved documents. Supports search depth, topic scoping (general/news), domain inclusion/exclusion, and toggles for raw content.

</div>
<div style="flex: 0 0 300px;"><img src="../../../images/previews/agents/websearchtavily.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node to fetch up-to-date web information for research, summaries, or grounding downstream reasoning. Typical workflow: provide a clear query, choose depth (basic/advanced), set result limits, optionally scope to news with a recent-days window, and refine with include/exclude domain lists. Feed the 'documents' output into parsers, summarizers, or retrieval chains; use 'answer' for quick overviews.

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
<tr><td style="word-wrap: break-word;">query</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Your search query. Provide a clear, specific request describing the information you need.</td><td style="word-wrap: break-word;">What are the latest developments in quantum error correction?</td></tr>
<tr><td style="word-wrap: break-word;">search_depth</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The depth of the search. Allowed values: basic (1 credit), advanced (2 credits). Advanced may retrieve broader/deeper coverage.</td><td style="word-wrap: break-word;">advanced</td></tr>
<tr><td style="word-wrap: break-word;">max_results</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Maximum number of search results to return. Minimum is 1.</td><td style="word-wrap: break-word;">5</td></tr>
<tr><td style="word-wrap: break-word;">topic</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Category of the search. Allowed values: general, news. Choose 'news' for recent coverage focused on current events.</td><td style="word-wrap: break-word;">news</td></tr>
<tr><td style="word-wrap: break-word;">days</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Only applies when topic is 'news'. Number of days back from today to include in results.</td><td style="word-wrap: break-word;">3</td></tr>
<tr><td style="word-wrap: break-word;">include_answer</td><td>True</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">If true, includes a short synthesized answer to the original query.</td><td style="word-wrap: break-word;">True</td></tr>
<tr><td style="word-wrap: break-word;">include_raw_content</td><td>True</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">If true, includes raw text content of search results. If false, only the most query-relevant extracted content is returned.</td><td style="word-wrap: break-word;">False</td></tr>
<tr><td style="word-wrap: break-word;">include_domains</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Domains to specifically include in results. Provide one domain per line.</td><td style="word-wrap: break-word;">nature.com science.org arxiv.org</td></tr>
<tr><td style="word-wrap: break-word;">exclude_domains</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Domains to exclude from results. Provide one domain per line.</td><td style="word-wrap: break-word;">wikipedia.org reddit.com</td></tr>
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
<tr><td style="word-wrap: break-word;">answer</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">A concise answer to the query, when available and include_answer is enabled.</td><td style="word-wrap: break-word;">Quantum error correction has advanced with new bosonic codes reducing overhead and demonstrations of logical qubit lifetimes exceeding physical qubits in 2025 studies.</td></tr>
<tr><td style="word-wrap: break-word;">documents</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">A JSON-formatted string containing an array of document objects returned by the search (titles, URLs, snippets, and optionally raw content).</td><td style="word-wrap: break-word;">[   {     "title": "Breakthrough in Quantum Error Correction",     "url": "https://example.com/article",     "snippet": "Researchers report improved logical qubit fidelity...",     "content": "..."    } ]</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Query must be non-empty**: An empty query will raise an error.
- **News-only window**: The 'days' parameter only affects searches when topic is set to 'news'.
- **Answer availability**: If include_answer is disabled or no answer is produced, the 'answer' output may be empty.
- **Domain filters**: Enter one domain per line for include/exclude lists; leave empty to not constrain by domains.
- **Result format**: 'documents' is a JSON string; parse it before downstream processing if you need structured access.
- **Search depth costs**: 'advanced' depth uses more credits than 'basic'.

## Troubleshooting
- **Empty or missing results**: Increase max_results, switch to 'advanced' search_depth, broaden the query, or remove restrictive domain filters.
- **No answer returned**: Ensure include_answer is true and try 'advanced' depth; some queries may not yield a synthesized answer.
- **Stale or irrelevant results**: Use topic='news' with an appropriate 'days' value to focus on recent content, or specify include_domains.
- **Errors about empty query**: Provide a non-empty 'query' string.
- **Network or service errors**: Retry later; verify your Salt Agents service configuration and connectivity.
- **Downstream parsing errors**: Remember 'documents' is a JSON string; parse it before attempting to access fields.

## Example Pipelines

<figure style="margin: 20px 0;">
  <img src="../../../images/assets/agents/websearchtavily/example.png" alt="Example" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" />
  <figcaption style="margin-top: 8px; text-align: center; font-style: italic; color: #666;">Example</figcaption>
</figure>

