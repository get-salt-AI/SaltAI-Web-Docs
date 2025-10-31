# OpenFDA Drug Product Label

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Queries the OpenFDA Drug Label endpoint to retrieve labeling information such as warnings, adverse reactions, and precautions. Supports both search and count actions with optional exact matching. Returns results and response metadata as JSON strings.

</div>
<div style="flex: 0 0 300px;"><img src="../../../images/previews/new-uncategorized/openfdadproductlabelnode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need authoritative drug labeling data from the FDA, e.g., to look up warnings, adverse reactions, or instructions for use for a specific drug. Typical workflow: set a target field (like "warnings"), provide a search term (e.g., a drug name), choose between search or count, optionally enable exact match, and set a result limit. The outputs can be fed into downstream parsing, analysis, or display nodes.

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
<tr><td style="word-wrap: break-word;">field_value</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The value to query for within the selected field (e.g., a drug name or keyword).</td><td style="word-wrap: break-word;">aspirin</td></tr>
<tr><td style="word-wrap: break-word;">field</td><td>True</td><td style="word-wrap: break-word;">ENUM</td><td style="word-wrap: break-word;">The product label field to query. Options map to OpenFDA label fields.</td><td style="word-wrap: break-word;">warnings</td></tr>
<tr><td style="word-wrap: break-word;">action</td><td>True</td><td style="word-wrap: break-word;">ENUM</td><td style="word-wrap: break-word;">Choose between 'search' to retrieve matching records or 'count' to get aggregated counts for the selected field.</td><td style="word-wrap: break-word;">search</td></tr>
<tr><td style="word-wrap: break-word;">exact_match</td><td>True</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">If true, performs an exact match against the selected field. If false, uses a broader query.</td><td style="word-wrap: break-word;">false</td></tr>
<tr><td style="word-wrap: break-word;">limit</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Maximum number of results to return (min 1). For 'count', limits the number of buckets returned by the API.</td><td style="word-wrap: break-word;">5</td></tr>
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
<tr><td style="word-wrap: break-word;">results</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">JSON string of the OpenFDA results array for the query.</td><td style="word-wrap: break-word;">[{...}, {...}]</td></tr>
<tr><td style="word-wrap: break-word;">metadata</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">JSON string of the OpenFDA response metadata (e.g., paging, disclaimers).</td><td style="word-wrap: break-word;">{"disclaimer": "...", "results": {"skip": 0, "limit": 5, "total": 1234}}</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Field options**: The selectable fields include abuse, adverse reactions, components, drug interactions, effective time, general_precautions, instructions for use, nursing_mothers, overdosage, precautions, and warnings.
- **Exact match behavior**: Enabling exact_match applies an exact match query on the chosen field, which may significantly narrow results.
- **API endpoint**: Queries the OpenFDA Drug Label endpoint (/drug/label.json).
- **Rate limits**: The OpenFDA API enforces rate limiting; frequent or large queries may be throttled.
- **Output format**: Both outputs are JSON strings; parse them downstream if structured access is needed.

## Troubleshooting
- **Empty results**: Try disabling exact_match, adjusting the field, or using a broader field_value.
- **Network error**: If the API is unreachable or times out, the node returns an error JSON string. Retry later or check connectivity.
- **JSON decode error**: Indicates an unexpected response format; try again or validate the API status.
- **Unexpected field behavior**: Ensure the selected field aligns with the intended OpenFDA label attribute; switching fields (e.g., from warnings to adverse reactions) can improve results.
- **Count results interpretation**: When using 'count', remember the results represent aggregated buckets; 'limit' controls how many buckets are returned.
