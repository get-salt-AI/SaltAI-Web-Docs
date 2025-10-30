# Google Web Search

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Runs a Google web search and returns the found documents as a JSON-formatted string. Supports common Google query notations (quotes for exact match, minus for exclusion, site:, filetype:, related:) and allows limiting results, applying safe search, and constraining by time.

</div>
<div style="flex: 0 0 300px;"><img src="../../../images/previews/Agents/WebSearchGoogle.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need up-to-date information from the public web via Google. Provide a clear, non-empty query, optionally refine with Google search operators, choose how many results to retrieve, and apply time or safety filters as needed. Connect its output to nodes that can parse or consume a JSON string of documents for further summarization, extraction, or analysis.

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
<tr><td style="word-wrap: break-word;">query</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The search query. Supports Google-style operators: exact match with quotes, exclude terms with '-', restrict to a domain with site:, restrict by file type with filetype:, find related sites with related:.</td><td style="word-wrap: break-word;">"large language models" -advertising site:arxiv.org filetype:pdf</td></tr>
<tr><td style="word-wrap: break-word;">max_results</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Maximum number of search results to return. Range 1–10.</td><td style="word-wrap: break-word;">5</td></tr>
<tr><td style="word-wrap: break-word;">safesearch</td><td>True</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">Enable content filtering for safer results.</td><td style="word-wrap: break-word;">true</td></tr>
<tr><td style="word-wrap: break-word;">timelimit</td><td>True</td><td style="word-wrap: break-word;">CHOICE</td><td style="word-wrap: break-word;">Time window unit for results. Choose None (no limit), d (days), w (weeks), m (months), or y (years). Use together with timelimit_number.</td><td style="word-wrap: break-word;">w</td></tr>
<tr><td style="word-wrap: break-word;">timelimit_number</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Number of time units to apply when timelimit is set. For example, timelimit = w and timelimit_number = 2 filters to the last 2 weeks.</td><td style="word-wrap: break-word;">2</td></tr>
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
<tr><td style="word-wrap: break-word;">documents</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">A JSON-formatted string containing the list of retrieved documents. Each entry typically includes title, URL, snippet, and other metadata provided by the search service.</td><td style="word-wrap: break-word;">[{"title": "Paper Title", "url": "https://example.com", "snippet": "..."}]</td></tr>
</tbody>
</table>
</div>

## Important Notes
- The query must be a non-empty string; empty queries will fail.
- The output is a STRING containing JSON. Downstream nodes may need to parse this string into structured data.
- max_results is capped at 10. If you request more, only up to 10 will be returned.
- Time filtering requires both timelimit and timelimit_number. Set timelimit to None to disable time constraints.
- Safe search may reduce or alter result visibility; disable it if you need broader coverage (where appropriate).
- Network access is required and external service availability may affect results and latency.

## Troubleshooting
- Query is empty: Ensure the query field contains non-whitespace text.
- Too few or no results: Increase timelimit range, disable or relax safesearch, or broaden the query terms.
- Results seem outdated: Set an appropriate timelimit and timelimit_number to focus on recent content.
- Output parsing errors downstream: Remember the output is a JSON string; parse it before accessing document fields.
- Hitting the results cap: max_results cannot exceed 10. If you need more, run multiple queries or paginate logically.
- Request errors or timeouts: Check network connectivity and try again; consider simplifying the query or reducing max_results.

## Example Pipelines

<figure style="margin: 20px 0;">
  <img src="../../../images/assets/Agents/WebSearchGoogle/Example.png" alt="Example" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" />
  <figcaption style="margin-top: 8px; text-align: center; font-style: italic; color: #666;">Example</figcaption>
</figure>

