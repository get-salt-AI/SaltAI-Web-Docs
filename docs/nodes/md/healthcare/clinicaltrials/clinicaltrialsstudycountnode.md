# ClinicalTrials Study Count

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Retrieves a count of clinical studies from ClinicalTrials.gov that match a given condition or disease. Returns the result content and a metadata summary, supporting JSON or CSV output formats with basic error handling for network and parsing issues.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/healthcare/clinicaltrials/clinicaltrialsstudycountnode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need the number of studies that match a search term on ClinicalTrials.gov, optionally limiting returned fields. Typical workflow: provide a condition or disease query, choose output format (JSON recommended), optionally specify fields, then feed the count/metadata to subsequent filtering, reporting, or visualization steps.

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
<tr><td style="word-wrap: break-word;">condition_or_disease</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Primary search query for the condition, disease, or term to match studies on ClinicalTrials.gov.</td><td style="word-wrap: break-word;">diabetes</td></tr>
<tr><td style="word-wrap: break-word;">format</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Response format for the results content. Supported values: json, csv. JSON provides structured data including totalStudies; CSV returns raw text content.</td><td style="word-wrap: break-word;">json</td></tr>
<tr><td style="word-wrap: break-word;">fields</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Comma-separated list of fields to include in the response. Leave empty to use API defaults.</td><td style="word-wrap: break-word;">NCTId,BriefTitle,OverallStatus</td></tr>
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
<tr><td style="word-wrap: break-word;">results</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The response content. For format=json, a JSON string including the API response and a totalStudies summary; for format=csv, the raw CSV text.</td><td style="word-wrap: break-word;">{   "data": { ... },   "query": "diabetes",   "total_studies": 1234,   "min_rank": 1,   "max_rank": 10,   "api_version": "2.0" }</td></tr>
<tr><td style="word-wrap: break-word;">metadata</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Metadata about the request/response. For JSON: includes query, format, and api_version. For CSV: includes query, format, and content_length.</td><td style="word-wrap: break-word;">{   "query": "diabetes",   "format": "json",   "api_version": "2.0" }</td></tr>
</tbody>
</table>
</div>

## Important Notes
- The node queries the ClinicalTrials.gov API v2 using a condition/disease search and summarizes counts based on the API response.
- Supported formats are json and csv; xml is not supported by this node.
- When format=json, the node returns a JSON string with totalStudies and other summary fields; when format=csv, it returns raw CSV text.
- If no studies match the query, totalStudies may be 0 and the results content may be minimal.
- Network timeouts and errors are handled by returning an error JSON string in both outputs.

## Troubleshooting
- Network error: Ensure internet connectivity and that ClinicalTrials.gov API is reachable. The node returns an error string like {"error": "Network error"}. Retry later or adjust timeout upstream if applicable.
- JSON decode error: If format=json and the API returns non-JSON content, you will receive {"error": "JSON decode error"}. Switch to CSV or try again later.
- Empty or unexpected results: Verify the condition_or_disease query string and try simplifying it. Check that fields are valid API fields when provided.
- CSV output looks truncated: Check the metadata output's content_length to confirm size. Long outputs may need downstream handling for display or storage.
