# ClinicalTrials Study Count

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Queries ClinicalTrials.gov (API v2) to retrieve a count summary of studies that match a given condition or disease term. Returns results and metadata as strings, with JSON providing aggregated fields like totalStudies, minRank, maxRank, and apiVersion. Supports raw CSV output when requested.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/healthcare/clinicaltrials/clinicaltrialsstudycountnode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node early in a research workflow to estimate the number of relevant clinical studies for a condition before fetching full records. It’s useful for gating decisions (e.g., whether to paginate through results) or to log summary metrics about search scope.

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
<tr><td style="word-wrap: break-word;">condition_or_disease</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Search term for the condition or disease (maps to query.cond in the API).</td><td style="word-wrap: break-word;">diabetes</td></tr>
<tr><td style="word-wrap: break-word;">format</td><td>True</td><td style="word-wrap: break-word;">["json", "csv"]</td><td style="word-wrap: break-word;">Response format. JSON returns a structured summary; CSV returns raw text content.</td><td style="word-wrap: break-word;">json</td></tr>
<tr><td style="word-wrap: break-word;">fields</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Comma-separated fields to include in the API response. Optional for count purposes; primarily affects the response payload when requesting non-count data.</td><td style="word-wrap: break-word;">NCTId,BriefTitle,Condition</td></tr>
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
<tr><td style="word-wrap: break-word;">results</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The main response content. For JSON format, a JSON string containing the API response and a count summary; for CSV, the raw CSV text.</td><td style="word-wrap: break-word;">{   "data": { ... },   "query": "diabetes",   "total_studies": 1234,   "min_rank": 1,   "max_rank": 10,   "api_version": "2.0" }</td></tr>
<tr><td style="word-wrap: break-word;">metadata</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Supplementary information about the request and response. For JSON, includes query, format, and API version; for CSV, includes format, content_length, and query.</td><td style="word-wrap: break-word;">{   "query": "diabetes",   "format": "json",   "api_version": "2.0" }</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Format options**: Only json and csv are supported as input options.
- **Network dependency**: Requires internet access to ClinicalTrials.gov API v2 at https://clinicaltrials.gov/api/v2.
- **Error handling**: On failure, both outputs are JSON strings with an "error" field (e.g., "Network error", "JSON decode error", or "Unknown error").
- **Fields parameter**: Passed through to the API as fields; for count purposes it typically does not affect the total count but can influence payload content in some responses.
- **Timeout**: The API request uses a 30-second timeout.
- **Output type**: Both outputs are strings; parse the JSON string if you need to programmatically access total_studies and related fields.

## Troubleshooting
- **Empty or zero count**: If total_studies is 0, verify the condition_or_disease term and try broader or alternative terminology.
- **Network error**: Check internet connectivity, firewall/proxy settings, or retry later; the node returns an error JSON string in both outputs on network failures.
- **Invalid format input**: Ensure format is set to "json" or "csv"; other values are not supported.
- **Large CSV content**: If CSV output is very large, consider switching to JSON format or narrowing the query.
- **Unexpected JSON structure**: If parsing fails, inspect the results string first; the node returns a JSON-formatted string for JSON mode and raw text for CSV.
