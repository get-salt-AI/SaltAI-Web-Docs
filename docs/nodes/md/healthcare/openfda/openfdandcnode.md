# OpenFDA NDC Directory

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Queries the OpenFDA National Drug Code (NDC) Directory for drug product information. You can search or count results by common drug attributes (e.g., brand name, generic name, dosage form, route, active ingredients) with optional exact matching. Returns result records and request metadata as JSON strings.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/healthcare/openfda/openfdandcnode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node to look up drug products in the NDC Directory by specific attributes or to count matches. Typical workflow: set a field (e.g., brand name), provide a value (e.g., "aspirin"), choose search or count, and optionally enable exact match. The node outputs JSON stringified results and metadata that you can pass to downstream nodes for parsing, display, or further analysis.

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
<tr><td style="word-wrap: break-word;">field_value</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The value to match against the selected field (e.g., a brand or generic name).</td><td style="word-wrap: break-word;">aspirin</td></tr>
<tr><td style="word-wrap: break-word;">field</td><td>True</td><td style="word-wrap: break-word;">CHOICE</td><td style="word-wrap: break-word;">Which NDC attribute to query. Options map to OpenFDA fields.</td><td style="word-wrap: break-word;">brand name</td></tr>
<tr><td style="word-wrap: break-word;">action</td><td>True</td><td style="word-wrap: break-word;">CHOICE</td><td style="word-wrap: break-word;">Select 'search' to retrieve matching records or 'count' to retrieve counts grouped by the selected field/value.</td><td style="word-wrap: break-word;">search</td></tr>
<tr><td style="word-wrap: break-word;">exact_match</td><td>True</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">When true, performs exact matching on the selected field.</td><td style="word-wrap: break-word;">false</td></tr>
<tr><td style="word-wrap: break-word;">limit</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Maximum number of results to return. Minimum is 1.</td><td style="word-wrap: break-word;">10</td></tr>
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
<tr><td style="word-wrap: break-word;">results</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">JSON string of the OpenFDA API 'results' array matching the query.</td><td style="word-wrap: break-word;">[{"product_ndc": "12345-6789", "brand_name": "ASPIRIN", ...}]</td></tr>
<tr><td style="word-wrap: break-word;">metadata</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">JSON string of the OpenFDA API 'meta' response including query info and result count.</td><td style="word-wrap: break-word;">{"disclaimer": "...", "results": {"skip": 0, "limit": 10, "total": 123}}</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Field options and mappings**: Available field choices include 'active ingredient name' (active_ingredients.name), 'active ingredient strength' (active_ingredients.strength), 'brand name' (brand_name), 'generic name' (generic_name), 'dosage form' (dosage_form), 'route' (route), and 'packaging description' (packaging.description).
- **Exact matching behavior**: When exact_match is true, the node queries the '.exact' variant of the selected field.
- **Endpoint**: Queries the OpenFDA NDC endpoint (/drug/ndc.json).
- **Output format**: Both outputs are JSON strings. Parse them if you need structured objects downstream.
- **Minimum limit**: limit must be at least 1.
- **Error handling**: On errors, the node returns stringified JSON with an 'error' field (e.g., {"error": "Network error"}).

## Troubleshooting
- **No results returned**: Try disabling exact_match, verify the field/value combination, or increase the limit.
- **Received an error JSON**: Check network connectivity and retry. The node returns {"error": "Network error"}, {"error": "JSON decode error"}, or {"error": "Unknown error"} on failures.
- **Unexpected counts or results**: Ensure the correct field is selected for your query context and that the field_value reflects the expected formatting (e.g., ingredient name vs. strength).
- **Downstream parsing issues**: Remember outputs are JSON strings. Parse them before attempting to access fields.
