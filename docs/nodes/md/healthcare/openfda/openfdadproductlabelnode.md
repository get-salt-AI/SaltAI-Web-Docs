# OpenFDA Drug Product Label

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Queries the OpenFDA Drug Product Label endpoint to retrieve labeling sections such as warnings, adverse reactions, overdosage, and more. Supports search and count actions with optional exact matching and a configurable result limit. Returns JSON-formatted results and metadata as strings.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/healthcare/openfda/openfdadproductlabelnode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need authoritative drug labeling text from OpenFDA (e.g., warnings or adverse reactions) for a specific substance or product attribute. Typical workflow: choose a labeling field (e.g., warnings), provide a search value (e.g., aspirin), select action (search to retrieve documents or count to aggregate), optionally require exact match, set a limit, then pass the JSON outputs downstream for parsing or display.

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
<tr><td style="word-wrap: break-word;">field_value</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The term or phrase to search for within the chosen labeling field.</td><td style="word-wrap: break-word;">aspirin</td></tr>
<tr><td style="word-wrap: break-word;">field</td><td>True</td><td style="word-wrap: break-word;">CHOICE</td><td style="word-wrap: break-word;">The labeling section to query. Options include: abuse, adverse reactions, components, drug interactions, effective time, general_precautions, instructions for use, nursing_mothers, overdosage, precautions, warnings.</td><td style="word-wrap: break-word;">warnings</td></tr>
<tr><td style="word-wrap: break-word;">action</td><td>True</td><td style="word-wrap: break-word;">CHOICE</td><td style="word-wrap: break-word;">Whether to retrieve documents (search) or return counts grouped by the specified field (count).</td><td style="word-wrap: break-word;">search</td></tr>
<tr><td style="word-wrap: break-word;">exact_match</td><td>True</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">If true, performs an exact match search against the selected field.</td><td style="word-wrap: break-word;">false</td></tr>
<tr><td style="word-wrap: break-word;">limit</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Maximum number of results to return. Applies to both search and count actions.</td><td style="word-wrap: break-word;">5</td></tr>
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
<tr><td style="word-wrap: break-word;">results</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">JSON string of the API results. For action=search, this is an array of label documents; for action=count, this is an array of count buckets.</td><td style="word-wrap: break-word;">[{"id": "XXXXXXXX", "warnings": ["..."], "adverse_reactions": ["..."]}]</td></tr>
<tr><td style="word-wrap: break-word;">metadata</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">JSON string with OpenFDA response metadata (e.g., disclaimers, license, last_updated, results info).</td><td style="word-wrap: break-word;">{"disclaimer": "...", "results": {"skip": 0, "limit": 5, "total": 1234}}</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Endpoint**: Queries OpenFDA Drug Label endpoint at /drug/label.json.
- **Field mapping**: The chosen field corresponds to specific OpenFDA fields (e.g., "warnings" -> warnings, "adverse reactions" -> adverse_reactions).
- **Exact match behavior**: When exact_match is true, searches use an exact term match, which can reduce the number of results but increase precision.
- **Defaults**: If not changed, action defaults to search, exact_match defaults to false, and limit defaults to 1.
- **Rate limits and availability**: OpenFDA imposes rate limits and may experience intermittent availability; plan retries or backoff in larger workflows.
- **Output format**: Both outputs are JSON strings; downstream nodes may need to parse them into structured data.

## Troubleshooting
- **Empty results**: Try disabling exact_match, broaden the field_value, or increase limit. Verify the selected field is appropriate for your query.
- **Count returns unexpected buckets**: Ensure the field chosen is suitable for aggregation and that field_value isn't over-constraining the search.
- **Network error**: Check internet connectivity and try again. If the API is rate-limited, wait and retry with a lower frequency.
- **JSON decode error**: The API may have returned malformed or unexpected content temporarily; retry the request or validate the endpoint.
- **Unknown error**: Re-run with simpler parameters (e.g., action=search, exact_match=false, small limit). If persistent, verify OpenFDA service status.
