# ClinicalTrials Study Count

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Queries ClinicalTrials.gov (API v2) to retrieve results metadata for studies matching a condition or disease query. When format is json, the node returns a JSON string that includes the original API payload plus helpful summary fields such as total_studies, min_rank, max_rank, and api_version. For non-JSON formats, it returns the raw response text alongside minimal metadata.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/healthcare/clinicaltrials/clinicaltrialsstudycountnode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node to quickly estimate how many clinical studies match a search term before running more detailed retrieval steps. Typical workflow: supply a condition/disease term (e.g., "breast cancer"), choose output format (json recommended), optionally specify fields to limit the returned fields, and then branch logic based on total_studies.

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
<tr><td style="word-wrap: break-word;">condition_or_disease</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Primary search query for matching studies. This is sent as query.cond to the ClinicalTrials.gov API.</td><td style="word-wrap: break-word;">breast cancer</td></tr>
<tr><td style="word-wrap: break-word;">format</td><td>True</td><td style="word-wrap: break-word;">CHOICE</td><td style="word-wrap: break-word;">Response format. json returns a processed JSON string with summary fields; csv returns raw CSV text.</td><td style="word-wrap: break-word;">json</td></tr>
<tr><td style="word-wrap: break-word;">fields</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Optional, comma-separated list of fields to include from the API. If empty, the API default fields are returned.</td><td style="word-wrap: break-word;">NCTId,BriefTitle,OverallStatus</td></tr>
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
<tr><td style="word-wrap: break-word;">results</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">For json format: a JSON string including the API response and summary fields (total_studies, min_rank, max_rank, api_version). For csv format: raw CSV text.</td><td style="word-wrap: break-word;">{   "data": { ... },   "query": "breast cancer",   "total_studies": 1245,   "min_rank": 1,   "max_rank": 10,   "api_version": "2.0" }</td></tr>
<tr><td style="word-wrap: break-word;">metadata</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">A JSON string with minimal metadata about the request/response. For json format: includes query, format, api_version. For csv format: includes query, format, and content_length.</td><td style="word-wrap: break-word;">{   "query": "breast cancer",   "format": "json",   "api_version": "2.0" }</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Formats**: The node supports json and csv via the format input. While internal comments may mention xml, it is not exposed as a selectable format.
- **Summary fields in JSON**: When format is json, total_studies, min_rank, and max_rank are extracted from the API response. If not present, sensible defaults are used (e.g., 0 for total_studies).
- **Fields filter**: Providing fields narrows the API return fields; ensure field names are valid for ClinicalTrials.gov API v2.
- **Network dependency**: This node requires internet access and depends on the ClinicalTrials.gov public API. API availability or rate limits can affect results.
- **Error handling**: On errors, both outputs return JSON strings with an "error" key (e.g., {"error": "Network error"}).
- **Endpoint**: Queries the /studies endpoint with query.cond constructed from condition_or_disease.

## Troubleshooting
- **Empty or very low total_studies**: Verify your condition_or_disease term, try broader keywords, or remove restrictive fields filters.
- **Network error**: Check internet connectivity and try again. If the issue persists, the ClinicalTrials.gov API may be experiencing downtime or rate limiting.
- **JSON decode error**: Ensure format is set to json. If the API returned non-JSON content due to an upstream issue, try again or switch to csv temporarily.
- **Unexpected CSV content**: If format is csv but content seems malformed, review the fields parameter and query term for invalid characters or try the default (no fields).
- **Missing fields in JSON**: If total_studies or other summary fields are missing, they may not be provided by the API for that response; the node supplies defaults.
