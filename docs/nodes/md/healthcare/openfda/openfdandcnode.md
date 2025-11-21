# OpenFDA NDC Directory

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Queries the OpenFDA National Drug Code (NDC) Directory to search or count records matching a specified field and value. Supports both fuzzy and exact matching and returns the API results along with response metadata. Useful for discovering drug products by brand, generic name, dosage form, route, ingredients, and packaging details.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/healthcare/openfda/openfdandcnode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need to look up drug product information from the FDA's NDC Directory. Select a field to search (e.g., brand name), provide a value (e.g., "aspirin"), choose whether to search or count, and optionally toggle exact matching. This node is typically placed early in a workflow to fetch data that will be parsed or analyzed by subsequent nodes.

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
<tr><td style="word-wrap: break-word;">field_value</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The value to search for within the selected field. If exact_match is enabled, the value must match exactly.</td><td style="word-wrap: break-word;">aspirin</td></tr>
<tr><td style="word-wrap: break-word;">field</td><td>True</td><td style="word-wrap: break-word;">CHOICE</td><td style="word-wrap: break-word;">Which NDC Directory field to query. Available options map to OpenFDA fields: active ingredient name → active_ingredients.name, active ingredient strength → active_ingredients.strength, brand name → brand_name, generic name → generic_name, dosage form → dosage_form, route → route, packaging description → packaging.description.</td><td style="word-wrap: break-word;">brand name</td></tr>
<tr><td style="word-wrap: break-word;">action</td><td>True</td><td style="word-wrap: break-word;">CHOICE</td><td style="word-wrap: break-word;">Select the query mode. 'search' returns matching records. 'count' returns aggregation/counts for the selected field and value.</td><td style="word-wrap: break-word;">search</td></tr>
<tr><td style="word-wrap: break-word;">exact_match</td><td>True</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">If true, performs an exact match against the selected field; otherwise performs a broader term search.</td><td style="word-wrap: break-word;">false</td></tr>
<tr><td style="word-wrap: break-word;">limit</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Maximum number of results to return. Minimum is 1. Higher values may be constrained by API limits.</td><td style="word-wrap: break-word;">5</td></tr>
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
<tr><td style="word-wrap: break-word;">results</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">JSON string containing the list of matched records or counts, depending on the action.</td><td style="word-wrap: break-word;">{"results": [{"brand_name": "ASPIRIN", "generic_name": "ASPIRIN"}]}</td></tr>
<tr><td style="word-wrap: break-word;">metadata</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">JSON string containing the OpenFDA response metadata, such as request details and results paging info.</td><td style="word-wrap: break-word;">{"meta": {"results": {"skip": 0, "limit": 5}}}</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Field options**: The displayed field names map to OpenFDA API fields as documented in the input description.
- **Search vs. Count**: 'search' returns records; 'count' returns aggregation results rather than full records.
- **Exact matching**: Enabling exact_match narrows results and may return zero matches if the value does not exactly match.
- **API constraints**: The underlying OpenFDA service may impose rate limits and maximum page sizes that can affect large queries.
- **Output format**: Both outputs are JSON strings; parse them in downstream nodes if you need to extract specific values.

## Troubleshooting
- **No results returned**: Try disabling exact_match, using a different field, or broadening the field_value.
- **Unexpected counts or empty aggregations**: Ensure 'action' is set correctly and that the field supports counting in the OpenFDA dataset.
- **Rate limit or network errors**: Reduce request frequency, lower the limit, and retry after a short delay.
- **Invalid field behavior**: Verify you selected a field from the provided options; custom fields are not supported in this node.
