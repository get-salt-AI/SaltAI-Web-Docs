# Google Web Search

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Runs a web search using the Google endpoint of the Agents service and returns the search results as a JSON-formatted string. Supports common Google search notations like exact phrases, exclusions, site/domain limits, file types, and related sites. Designed for fetching a limited set of top results with optional safe search and time range filtering.

</div>
<div style="flex: 0 0 300px;"><img src="../../../images/previews/agents/websearchgoogle.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node to retrieve web documents relevant to a query from Google, then pass the returned JSON string into downstream parsing or RAG pipelines. Typical workflow: provide a precise query (optionally with Google operators), set max_results and safesearch, optionally constrain by recent time windows, then parse the 'documents' JSON for titles, URLs, and snippets.

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
<tr><td style="word-wrap: break-word;">query</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The search query. Supports Google operators: "exact phrase", -exclude, site:domain.com, filetype:ext, related:domain.com. Must be a non-empty string.</td><td style="word-wrap: break-word;">site:arxiv.org "diffusion models" filetype:pdf</td></tr>
<tr><td style="word-wrap: break-word;">max_results</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Maximum number of results to return. Must be between 1 and 10.</td><td style="word-wrap: break-word;">5</td></tr>
<tr><td style="word-wrap: break-word;">safesearch</td><td>True</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">Enable or disable safe search filtering of explicit content.</td><td style="word-wrap: break-word;">true</td></tr>
<tr><td style="word-wrap: break-word;">timelimit</td><td>True</td><td style="word-wrap: break-word;">CHOICE</td><td style="word-wrap: break-word;">Time window for results. Choose one of: None, d (day), w (week), m (month), y (year).</td><td style="word-wrap: break-word;">w</td></tr>
<tr><td style="word-wrap: break-word;">timelimit_number</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Number of time units when timelimit is set. For example, timelimit=w and timelimit_number=2 restricts results to the last 2 weeks.</td><td style="word-wrap: break-word;">2</td></tr>
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
<tr><td style="word-wrap: break-word;">documents</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">A JSON-formatted string representing the list of search result documents returned by the Agents service. Typically includes fields like title, url, and content/snippet depending on service response.</td><td style="word-wrap: break-word;">[{"title": "Example Result", "url": "https://example.com", "snippet": "..."}]</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Non-empty query required**: The node will raise an error if 'query' is empty or only whitespace.
- **Output is a JSON string**: Downstream nodes may need to parse this string to access individual document fields.
- **Result count bounds**: 'max_results' must be between 1 and 10.
- **Time filter usage**: 'timelimit_number' only applies when 'timelimit' is not 'None'.
- **Safe search behavior**: 'safesearch' controls filtering at the service level; it may still return some borderline content depending on source behavior.
- **Network call and timeout**: The node performs an HTTP POST to the Agents service and may raise errors on timeouts or non-200 responses.

## Troubleshooting
- **Empty query error**: Ensure 'query' is a non-empty string without only whitespace.
- **Service error or non-200 response**: Check the Agents service availability and credentials/configuration; review the error message returned by the node.
- **JSON parsing downstream**: If a downstream step fails, ensure you parse the 'documents' output from STRING into structured data first.
- **No results returned**: Loosen the query, remove restrictive operators (like site: or filetype:), or increase 'max_results'.
- **Time window too narrow**: If using 'timelimit', increase 'timelimit_number' or set 'timelimit' to 'None' to broaden results.
- **Unexpected content despite safesearch**: Set 'safesearch' to true and refine the query terms; note that filtering depends on upstream sources and may not be absolute.

## Example Pipelines

<figure style="margin: 20px 0;">
  <img src="../../../images/assets/agents/websearchgoogle/example.png" alt="Example" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" />
  <figcaption style="margin-top: 8px; text-align: center; font-style: italic; color: #666;">Example</figcaption>
</figure>

