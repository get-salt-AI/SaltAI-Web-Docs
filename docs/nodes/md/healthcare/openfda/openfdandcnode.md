# OpenFDA NDC Directory

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Queries the OpenFDA NDC (National Drug Code) Directory API to retrieve product information such as brand name, generic name, dosage form, route, packaging, and active ingredient details. Returns results and metadata as JSON strings. Supports both search and count actions with optional exact matching.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/healthcare/openfda/openfdandcnode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need to look up drug products in the FDA’s NDC Directory by fields like brand name, generic name, or active ingredients. Commonly placed early in a workflow to fetch regulatory product data that can then be parsed, filtered, or displayed downstream.

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
<tr><td style="word-wrap: break-word;">field_value</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The value to query for in the selected field.</td><td style="word-wrap: break-word;">aspirin</td></tr>
<tr><td style="word-wrap: break-word;">field</td><td>True</td><td style="word-wrap: break-word;">CHOICE</td><td style="word-wrap: break-word;">Which NDC Directory field to search. Options map to specific OpenFDA fields.</td><td style="word-wrap: break-word;">brand name</td></tr>
<tr><td style="word-wrap: break-word;">action</td><td>True</td><td style="word-wrap: break-word;">CHOICE</td><td style="word-wrap: break-word;">Select 'search' to return matching records or 'count' to return aggregation counts for the selected field.</td><td style="word-wrap: break-word;">search</td></tr>
<tr><td style="word-wrap: break-word;">exact_match</td><td>True</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">If true, performs an exact match query on the selected field.</td><td style="word-wrap: break-word;">false</td></tr>
<tr><td style="word-wrap: break-word;">limit</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Maximum number of results to return. Minimum value is 1.</td><td style="word-wrap: break-word;">3</td></tr>
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
<tr><td style="word-wrap: break-word;">results</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">JSON string of the OpenFDA API 'results' array matching the query or count output.</td><td style="word-wrap: break-word;">[{"brand_name": "ASPIRIN", "generic_name": "ASPIRIN", "dosage_form": "TABLET"}]</td></tr>
<tr><td style="word-wrap: break-word;">metadata</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">JSON string of the OpenFDA API 'meta' object, including information like result count and request details.</td><td style="word-wrap: break-word;">{"disclaimer": "...", "results": {"skip": 0, "limit": 3, "total": 120}}</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Field options**: "active ingredient name", "active ingredient strength", "brand name", "generic name", "dosage form", "route", "packaging description".
- **Exact match behavior**: When exact_match is true, the query uses the '.exact' variant of the selected field for stricter matching.
- **Actions**: 'search' returns matching records; 'count' returns aggregated counts for the chosen field.
- **Rate limits and availability**: Subject to OpenFDA API rate limits and service availability.
- **Output format**: Both outputs are JSON strings; downstream nodes may need to parse them before further processing.

## Troubleshooting
- **Network error**: If the API is unreachable or times out, the node returns {"error": "Network error"} in both outputs. Check connectivity and retry.
- **JSON decode error**: If the API response cannot be parsed, the node returns {"error": "JSON decode error"}. Retry later or verify the API status.
- **Unknown error**: Returns {"error": "Unknown error"} on unexpected failures. Review input values and try a simpler query.
- **Empty results**: If 'results' is empty, confirm the field and field_value are valid for the NDC dataset or try disabling exact_match.
- **Too few/many results**: Adjust 'limit' for desired volume; for broad queries consider using 'count' first to explore data distribution.
