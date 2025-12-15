# OpenFDA Drug Product Label

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Queries the OpenFDA Drug Label endpoint to retrieve information from FDA-approved drug labeling. You can search or count results across specific label sections (e.g., warnings, adverse reactions) with optional exact matching and a configurable limit.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/healthcare/openfda/openfdadproductlabelnode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need structured information from FDA drug product labels, such as warnings, precautions, or adverse reactions. Typical workflow: choose the label section (field), set your search term (field_value), decide between search or count, optionally enable exact match for precise term lookups, and set the desired result limit. The node returns JSON-formatted results and metadata as strings suitable for downstream parsing or display.

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
<tr><td style="word-wrap: break-word;">field_value</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The search term or phrase to look for within the selected label field.</td><td style="word-wrap: break-word;">aspirin</td></tr>
<tr><td style="word-wrap: break-word;">field</td><td>True</td><td style="word-wrap: break-word;">CHOICE</td><td style="word-wrap: break-word;">The specific section of the drug label to query. Options map to OpenFDA label fields.</td><td style="word-wrap: break-word;">warnings</td></tr>
<tr><td style="word-wrap: break-word;">action</td><td>True</td><td style="word-wrap: break-word;">CHOICE</td><td style="word-wrap: break-word;">Select 'search' to retrieve matching records or 'count' to get aggregate counts grouped by the chosen field.</td><td style="word-wrap: break-word;">search</td></tr>
<tr><td style="word-wrap: break-word;">exact_match</td><td>True</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">If true, performs an exact match on the selected field (uses the field's .exact variant).</td><td style="word-wrap: break-word;">false</td></tr>
<tr><td style="word-wrap: break-word;">limit</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Maximum number of results to return. Must be 1 or greater.</td><td style="word-wrap: break-word;">3</td></tr>
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
<tr><td style="word-wrap: break-word;">results</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">JSON string of the OpenFDA API response results. For 'search', this is a list of matching records; for 'count', this is a list of count buckets.</td><td style="word-wrap: break-word;">[{"id": "...", "warnings": ["..."]}]</td></tr>
<tr><td style="word-wrap: break-word;">metadata</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">JSON string containing OpenFDA metadata (e.g., disclaimer, license, last_updated).</td><td style="word-wrap: break-word;">{"disclaimer": "...", "last_updated": "..."}</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Field options**: abuse, adverse reactions, components, drug interactions, effective time, general_precautions, instructions for use, nursing_mothers, overdosage, precautions, warnings.
- **Exact match behavior**: Enabling exact_match queries the '.exact' variant of the selected field, which can significantly reduce or refine results.
- **Action differences**: 'search' returns matching records; 'count' returns aggregated counts under results, not full records.
- **Output format**: Both outputs are JSON strings. Parse them as JSON before further structured processing.
- **Limits and availability**: The OpenFDA API may enforce rate limits or return empty results depending on data availability and your query parameters.

## Troubleshooting
- **No results returned**: Try disabling exact_match, choosing a different field, broadening field_value, or increasing limit.
- **Unexpected output shape with 'count'**: Remember that 'count' returns buckets (key/value pairs) under results, not full label documents.
- **Network or timeout errors**: Check network connectivity and retry later. The node returns JSON strings indicating an error when a network or parsing issue occurs.
- **Malformed JSON when parsing**: Ensure you are parsing the outputs as JSON strings. If an error JSON is returned, handle it gracefully before parsing into downstream steps.
