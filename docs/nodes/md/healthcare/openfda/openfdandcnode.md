# OpenFDA NDC Directory

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Queries the OpenFDA National Drug Code (NDC) Directory to retrieve drug product information. Supports searching or counting records by commonly used fields such as brand name, generic name, dosage form, route, and packaging details. Returns results and response metadata as JSON-formatted strings.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/healthcare/openfda/openfdandcnode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need authoritative drug product data from the FDA NDC Directory, such as looking up products by brand name or counting how many products match a specific dosage form. Typical workflow: provide a field value, choose the field to search, select whether to search or count, optionally enforce exact matching, and set a results limit. Connect the "results" output to downstream parsing or display nodes and the "metadata" output for pagination and request info.

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
<tr><td style="word-wrap: break-word;">field_value</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The value to search for within the selected field.</td><td style="word-wrap: break-word;">aspirin</td></tr>
<tr><td style="word-wrap: break-word;">field</td><td>True</td><td style="word-wrap: break-word;">one of ["active ingredient name", "active ingredient strength", "brand name", "generic name", "dosage form", "route", "packaging description"]</td><td style="word-wrap: break-word;">The NDC Directory field to query against.</td><td style="word-wrap: break-word;">brand name</td></tr>
<tr><td style="word-wrap: break-word;">action</td><td>True</td><td style="word-wrap: break-word;">one of ["search", "count"]</td><td style="word-wrap: break-word;">Select 'search' to retrieve matching records or 'count' to get aggregated counts for the selected field.</td><td style="word-wrap: break-word;">search</td></tr>
<tr><td style="word-wrap: break-word;">exact_match</td><td>True</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">If true, performs an exact match on the selected field; otherwise, performs a general search.</td><td style="word-wrap: break-word;">false</td></tr>
<tr><td style="word-wrap: break-word;">limit</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Maximum number of results to return. Must be >= 1.</td><td style="word-wrap: break-word;">10</td></tr>
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
<tr><td style="word-wrap: break-word;">results</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">JSON-formatted string containing the array of result records (for 'search') or count data (for 'count').</td><td style="word-wrap: break-word;">[{ "brand_name": "ASPIRIN", "generic_name": "ASPIRIN", "dosage_form": "TABLET" }]</td></tr>
<tr><td style="word-wrap: break-word;">metadata</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">JSON-formatted string with API response metadata, including request details, disclaimers, and pagination info when applicable.</td><td style="word-wrap: break-word;">{ "disclaimer": "openFDA is a beta research project...", "results": { "total": 1234, "limit": 10 } }</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Exact matching**: When exact_match is true, the search targets the field's exact value, which can reduce partial matches and improve precision.
- **Action differences**: 'search' returns matching records; 'count' returns aggregated counts for the chosen field.
- **Field mapping**: User-friendly field names map to OpenFDA fields (e.g., 'brand name' -> 'brand_name', 'dosage form' -> 'dosage_form').
- **Result format**: Both outputs are JSON strings; downstream nodes may need to parse them before further processing.
- **Limits and paging**: The limit input controls returned items. Use metadata to understand totals and plan pagination.
- **Rate limits and availability**: OpenFDA enforces rate limits and may throttle; network or service interruptions can occur.

## Troubleshooting
- **No results returned**: Verify field_value spelling and select the correct field. Try disabling exact_match or increasing limit.
- **Unexpected counts**: Ensure 'action' is set to 'count' and that the chosen field is appropriate for aggregation.
- **Network error**: Temporary connectivity or API issues may occur. Retry later and confirm internet access.
- **JSON decode error**: Downstream parsing failed. Ensure you are parsing the 'results' and 'metadata' as JSON strings with a proper parser.
- **Unknown error response**: Check inputs for invalid values and verify the OpenFDA service status. Try simplifying the query (e.g., remove exact_match or use a different field).
