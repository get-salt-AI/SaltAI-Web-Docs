# OpenFDA NDC Directory

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Queries the FDA's National Drug Code (NDC) Directory via the OpenFDA API. Supports search and count operations across common NDC attributes with optional exact-match filtering. Returns results and request metadata as JSON strings.

</div>
<div style="flex: 0 0 300px;"><img src="../../../images/previews/New-Uncategorized/OpenFDANDCNode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node to look up drugs in the FDA NDC Directory by brand, generic name, dosage form, route, or packaging attributes. Select a field, provide a value, choose search or count, and optionally enforce exact matching. The node outputs JSON strings suitable for downstream parsing, analysis, or display.

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
<tr><td style="word-wrap: break-word;">field_value</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The value to search for in the selected field. For example, a brand or generic name, a dosage form, or a route.</td><td style="word-wrap: break-word;">aspirin</td></tr>
<tr><td style="word-wrap: break-word;">field</td><td>True</td><td style="word-wrap: break-word;">select from: ["active ingredient name", "active ingredient strength", "brand name", "generic name", "dosage form", "route", "packaging description"]</td><td style="word-wrap: break-word;">The NDC Directory field to query against. These map to OpenFDA fields internally.</td><td style="word-wrap: break-word;">brand name</td></tr>
<tr><td style="word-wrap: break-word;">action</td><td>True</td><td style="word-wrap: break-word;">select from: ["search", "count"]</td><td style="word-wrap: break-word;">Choose 'search' to retrieve matching records or 'count' to aggregate counts by the selected field.</td><td style="word-wrap: break-word;">search</td></tr>
<tr><td style="word-wrap: break-word;">exact_match</td><td>True</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">If true, performs an exact match on the selected field. If false, performs a broader match.</td><td style="word-wrap: break-word;">false</td></tr>
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
<tr><td style="word-wrap: break-word;">results</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">JSON string of the OpenFDA API results for the query. For 'search', this is an array of records; for 'count', this is an array of count buckets.</td><td style="word-wrap: break-word;">[{ "brand_name": "ASPIRIN", "generic_name": "ASPIRIN", ... }]</td></tr>
<tr><td style="word-wrap: break-word;">metadata</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">JSON string containing metadata from the OpenFDA API response (e.g., query parameters and result set information).</td><td style="word-wrap: break-word;">{ "results": {"skip": 0, "limit": 10}, "disclaimer": "..." }</td></tr>
</tbody>
</table>
</div>

## Important Notes
- Exact match toggles an exact term query on the selected field, which is useful when you need precise results.
- The 'count' action aggregates results by the selected field and respects the limit as the number of buckets returned.
- Both outputs are JSON strings; parse them downstream if you need to extract fields.
- Supported fields map to OpenFDA NDC fields internally: active_ingredients.name, active_ingredients.strength, brand_name, generic_name, dosage_form, route, packaging.description.
- This node targets the OpenFDA /drug/ndc.json endpoint.

## Troubleshooting
- No results returned: Verify the field and field_value combination. Try disabling exact_match or adjusting the value to be less restrictive.
- Network error: Check internet connectivity and the OpenFDA API status; retry later.
- Malformed or unexpected output: Ensure downstream nodes parse the results and metadata as JSON strings before accessing fields.
- Count results seem limited: Increase the 'limit' to retrieve more count buckets.
- Unexpected matches with exact_match disabled: Enable exact_match to restrict results to exact term matches.
