# Google Web Search

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Runs a web search against the Google-backed endpoint and returns a JSON-formatted list of documents (as a string). Supports advanced query operators for precise filtering and can limit results by timeframe and safe search.

</div>
<div style="flex: 0 0 300px;"><img src="../../../images/previews/agents/websearchgoogle.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need relevant web pages, links, and snippets from the open web to feed into downstream tasks like summarization, RAG, or fact-checking. Provide a clear query (optionally with advanced operators), set max results (up to 10), and optionally constrain by safesearch and time window.

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
<tr><td style="word-wrap: break-word;">query</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The search query text. Supports advanced Google-like operators for precision.</td><td style="word-wrap: break-word;">site:example.com "machine learning" -tutorial filetype:pdf</td></tr>
<tr><td style="word-wrap: break-word;">max_results</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Maximum number of search results to return. Upper bound of 10.</td><td style="word-wrap: break-word;">5</td></tr>
<tr><td style="word-wrap: break-word;">safesearch</td><td>True</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">Enable or disable safe search filtering.</td><td style="word-wrap: break-word;">true</td></tr>
<tr><td style="word-wrap: break-word;">timelimit</td><td>True</td><td style="word-wrap: break-word;">['None', 'd', 'w', 'm', 'y']</td><td style="word-wrap: break-word;">Restricts results to a recent timeframe. d=day, w=week, m=month, y=year. Use 'None' for no time restriction.</td><td style="word-wrap: break-word;">w</td></tr>
<tr><td style="word-wrap: break-word;">timelimit_number</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Number of time units to apply when timelimit is set. For example, w + 2 returns results from the last 2 weeks.</td><td style="word-wrap: break-word;">2</td></tr>
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
<tr><td style="word-wrap: break-word;">documents</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">A JSON-formatted string containing the list of found documents (e.g., titles, URLs, and content snippets).</td><td style="word-wrap: break-word;">[{"title": "Intro to ML", "url": "https://example.com/intro", "snippet": "..."}]</td></tr>
</tbody>
</table>
</div>

## Important Notes
- The query must be a non-empty string; an empty query will raise an error.
- This node returns only a documents list (no 'answer' field).
- The result is a JSON string; parse it before downstream structured use.
- Advanced operators supported include: exact phrase ("term"), exclude (-term), site:domain.com, filetype:pdf, related:domain.com.
- Maximum results are capped at 10.
- A 60-second timeout applies to the underlying request.

## Troubleshooting
- Query is empty error: Ensure 'query' is not blank or whitespace.
- No results returned: Broaden the query, remove restrictive operators, or increase 'max_results'.
- Time filter not applied: Set both 'timelimit' (d/w/m/y) and 'timelimit_number' to specify the window.
- Non-200 response from service: Check service availability and your configuration; the node will raise an exception with the returned error detail.
- JSON parse issues downstream: Remember the output is a JSON string; parse it before using as structured data.
- Unexpected content due to safesearch: Toggle 'safesearch' to adjust filtering behavior.

## Example Pipelines

<figure style="margin: 20px 0;">
  <img src="../../../images/assets/agents/websearchgoogle/example.png" alt="Example" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" />
  <figcaption style="margin-top: 8px; text-align: center; font-style: italic; color: #666;">Example</figcaption>
</figure>

