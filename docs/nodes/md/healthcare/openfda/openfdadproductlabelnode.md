# OpenFDA Drug Product Label

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Queries the OpenFDA Drug Label endpoint to retrieve labeled drug information (e.g., warnings, adverse reactions, interactions). You can search or count results by specific label fields, optionally enforcing exact matches and limiting the number of returned items. Returns two JSON-formatted strings: one for results and one for API metadata.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/healthcare/openfda/openfdadproductlabelnode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need structured information from FDA drug product labels for downstream analytics, summarization, or compliance checks. Typical workflow: choose a label field (e.g., warnings), provide a value to look for (e.g., a substance or keyword), select whether to search or count, optionally enforce exact matches, and set a limit. Feed the results JSON into parsing or reporting nodes, and use the metadata JSON for paging or auditing.

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
<tr><td style="word-wrap: break-word;">field_value</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The value to search for within the selected label field. Used as the query term.</td><td style="word-wrap: break-word;">aspirin</td></tr>
<tr><td style="word-wrap: break-word;">field</td><td>True</td><td style="word-wrap: break-word;">One of: abuse, adverse reactions, components, drug interactions, effective time, general_precautions, instructions for use, nursing_mothers, overdosage, precautions, warnings</td><td style="word-wrap: break-word;">The drug label field to query against.</td><td style="word-wrap: break-word;">warnings</td></tr>
<tr><td style="word-wrap: break-word;">action</td><td>True</td><td style="word-wrap: break-word;">ENUM [search, count]</td><td style="word-wrap: break-word;">Choose 'search' to retrieve matching records or 'count' to get aggregated counts by the selected field.</td><td style="word-wrap: break-word;">search</td></tr>
<tr><td style="word-wrap: break-word;">exact_match</td><td>True</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">If true, the search is performed as an exact match for the selected field.</td><td style="word-wrap: break-word;">false</td></tr>
<tr><td style="word-wrap: break-word;">limit</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Maximum number of results (or count buckets) to return. Minimum is 1.</td><td style="word-wrap: break-word;">3</td></tr>
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
<tr><td style="word-wrap: break-word;">results</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">JSON-formatted string of the OpenFDA API 'results' payload matching the query.</td><td style="word-wrap: break-word;">[{ "id": "<record-id>", "warnings": ["..."], "adverse_reactions": ["..."] }]</td></tr>
<tr><td style="word-wrap: break-word;">metadata</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">JSON-formatted string of the OpenFDA API 'meta' object (e.g., information about request, results, and pagination).</td><td style="word-wrap: break-word;">{ "disclaimer": "...", "results": { "skip": 0, "limit": 3, "total": 123 } }</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Supported fields**: abuse, adverse reactions, components, drug interactions, effective time, general_precautions, instructions for use, nursing_mothers, overdosage, precautions, warnings.
- **Exact match behavior**: When enabled, the query uses an exact match variant of the selected field.
- **Limit**: Must be at least 1; use higher values responsibly to avoid large payloads.
- **Output format**: Both outputs are strings containing JSON. Downstream nodes expecting structured data may need to parse the JSON first.
- **External service**: This node relies on the public OpenFDA API. Availability, latency, and rate limits are governed by that service.

## Troubleshooting
- **Empty or unexpected results**: Verify the 'field' matches the intended label content and adjust 'field_value' or disable 'exact_match' if too restrictive.
- **Rate limiting or service errors**: If you encounter API errors or timeouts, reduce 'limit', try again later, or refine the query to return fewer results.
- **JSON handling issues downstream**: Remember outputs are JSON strings; ensure your next step parses the JSON before field-level access.
- **'count' action returns unexpected structure**: The API may format count results differently from search results. Inspect the 'results' JSON to confirm the structure and adjust parsing logic accordingly.
