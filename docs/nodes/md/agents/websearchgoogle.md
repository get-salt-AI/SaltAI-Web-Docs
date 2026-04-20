# Google Web Search

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

This node performs a web search using a Google-backed endpoint and returns the retrieved documents as a structured text payload. It supports common Google search notations (exact match, term exclusion, site restriction, file type filters, and related-site queries) plus options for safesearch and time-based filtering. The output is optimized for downstream nodes that summarize, analyze, or ground LLM responses in web data.

</div>
<div style="flex: 0 0 300px;"><img src="../../../images/previews/agents/websearchgoogle.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node whenever your workflow needs up-to-date information from the public web via Google-style search. Typical scenarios include: research assistants gathering sources, fact-checking pipelines, news or trend monitoring, and retrieval-augmented generation where web documents are appended to prompts.

Place Google Web Search after a node that generates or collects a user query (for example, a chat/agent node or a query-construction node). Connect its "documents" output to downstream components that can interpret or parse text collections, such as summarizers, custom RAG builders, or agents that reason over external knowledge. For recency-sensitive workflows (e.g., market updates, breaking news), configure "timelimit" and "timelimit_number" to constrain results to a recent window. For broad research, keep "timelimit" as "None" and adjust "max_results".

Good patterns: (1) Use Google operators like site:, filetype:pdf, and quoted phrases to precisely target types of content; (2) keep "max_results" modest (3–5) for responsive, low-latency interactions; (3) enable "safesearch" in user-facing assistants; and (4) pass "documents" through a parsing or chunking step before heavy LLM processing to control token usage.

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
<tr><td style="word-wrap: break-word;">query</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The search query string executed against the Google endpoint. Accepts natural language and common Google search operators: quotes for exact phrases ("climate change report"), minus sign to exclude terms (-sponsored), site restriction (site:who.int), file type filters (filetype:pdf), and related-site queries (related:example.com). Overly complex or noisy queries can reduce relevance; aim for concise, targeted expressions.</td><td style="word-wrap: break-word;">site:who.int "air quality guidelines" filetype:pdf -draft</td></tr>
<tr><td style="word-wrap: break-word;">max_results</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Maximum number of search results to retrieve. Must be an integer between 1 and 10. Higher values increase coverage but also latency and downstream processing cost; lower values return fewer but faster-to-handle results.</td><td style="word-wrap: break-word;">5</td></tr>
<tr><td style="word-wrap: break-word;">safesearch</td><td>True</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">Whether to apply a safe search filter to reduce explicit or sensitive content. When true, the search provider attempts to filter out adult or inappropriate material. This is best-effort and should be combined with additional moderation in sensitive applications.</td><td style="word-wrap: break-word;">True</td></tr>
<tr><td style="word-wrap: break-word;">timelimit</td><td>True</td><td style="word-wrap: break-word;">["None", "d", "w", "m", "y"]</td><td style="word-wrap: break-word;">Time range unit to restrict search results to recent content. Use "None" for no restriction, or "d" (days), "w" (weeks), "m" (months), or "y" (years). The actual length of the window is determined in combination with "timelimit_number". Only has effect when set to something other than "None".</td><td style="word-wrap: break-word;">w</td></tr>
<tr><td style="word-wrap: break-word;">timelimit_number</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Number of time units to use with "timelimit". For example, "timelimit" = "w" and "timelimit_number" = 2 limits results roughly to the last 2 weeks. Should be a positive integer; very large values reduce the benefit of filtering. Ignored when "timelimit" is "None".</td><td style="word-wrap: break-word;">2</td></tr>
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
<tr><td style="word-wrap: break-word;">documents</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">A single STRING containing a structured representation of the Google search results. Typically this is a text-encoded collection of items including URLs, titles, snippets, and possibly additional metadata, suitable for parsing or direct use as context in downstream nodes such as summarizers or retrieval pipelines.</td><td style="word-wrap: break-word;">[{"title": "WHO global air quality guidelines", "url": "https://www.who.int/publications/i/item/9789240034228", "snippet": "The WHO global air quality guidelines provide recommendations on air quality levels..."}, {"title": "Air quality guidelines - executive summary", "url": "https://apps.who.int/iris/handle/10665/345210", "snippet": "This executive summary presents key recommendations and rationale..."}]</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Performance**: Increasing "max_results" toward the upper limit (10) will increase latency and downstream token usage; for interactive use, start with 3–5.
- **Limitations**: The underlying Google integration may not match the behavior of consumer Google Search exactly (different ranking, localization, or personalization characteristics).
- **Behavior**: When "timelimit" is set to "None", "timelimit_number" has no effect even if set to a positive value, which can confuse debugging if you expect recency filtering.
- **Behavior**: The "documents" output is a single text blob (often JSON-like); if a downstream node expects structured objects or individual documents, insert a parsing or splitting step first.
- **Limitations**: Safesearch is best-effort and does not replace dedicated moderation; sensitive or undesired content may still appear and should be filtered downstream where necessary.

## Troubleshooting
- **Empty or very few results**: If "documents" is empty or nearly empty, relax your query (remove some operators, broaden keywords), lower recency constraints by setting "timelimit" to "None" or increasing "timelimit_number", and ensure "max_results" is at least 3.
- **Results are too old or not recent enough**: Confirm that "timelimit" is set to a non-"None" value (e.g., "w") and that "timelimit_number" is reasonable (such as 1–4). If still seeing older results, you may need to further constrain the query or rely on a more news-focused search strategy.
- **Unexpected content despite safesearch**: Verify that "safesearch" is set to true. If problematic pages still appear, refine the "query" to exclude certain terms (using -keyword) and add a content moderation or filtering node after Google Web Search.
- **Downstream nodes failing to parse documents**: Inspect the "documents" string in a logging or inspection node to confirm its structure. If the next node expects JSON or individual items, insert a dedicated parser or document-splitter to transform "documents" into the required format before passing it on.

## Example Pipelines

<figure style="margin: 20px 0;">
  <img src="../../../images/assets/agents/websearchgoogle/example.png" alt="Example" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" />
  <figcaption style="margin-top: 8px; text-align: center; font-style: italic; color: #666;">Example</figcaption>
</figure>

