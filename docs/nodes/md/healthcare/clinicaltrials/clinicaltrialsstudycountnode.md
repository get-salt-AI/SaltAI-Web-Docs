# ClinicalTrials Study Count

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Queries the ClinicalTrials.gov v2 API to retrieve the number of studies matching a condition or disease term. Returns parsed JSON with total study counts and related metadata when format is JSON, or raw CSV content when format is CSV.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/healthcare/clinicaltrials/clinicaltrialsstudycountnode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need the total number of clinical studies for a given condition, as a quick filter or to drive downstream logic (e.g., deciding whether to fetch detailed study data). Typical workflow: provide a search term (condition or disease), select the output format (JSON recommended for structured count values), optionally specify fields to include, and pass the results to reporting or further processing nodes.

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
<tr><td style="word-wrap: break-word;">condition_or_disease</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Primary search query for the ClinicalTrials.gov API. Commonly a disease or condition name.</td><td style="word-wrap: break-word;">lung cancer</td></tr>
<tr><td style="word-wrap: break-word;">format</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Output format for the API response. JSON returns structured data (including counts); CSV returns raw text.</td><td style="word-wrap: break-word;">json</td></tr>
<tr><td style="word-wrap: break-word;">fields</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Comma-separated list of fields to include in the response. Leave empty to use API defaults.</td><td style="word-wrap: break-word;">nctId,briefTitle,overallStatus</td></tr>
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
<tr><td style="word-wrap: break-word;">results</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The API response. For JSON format, a JSON string containing the full response plus extracted keys such as total_studies. For CSV format, the raw CSV text.</td><td style="word-wrap: break-word;">{   "data": {"studies": [...]},   "query": "lung cancer",   "total_studies": 1245,   "min_rank": 1,   "max_rank": 10,   "api_version": "2.0" }</td></tr>
<tr><td style="word-wrap: break-word;">metadata</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Metadata about the query and response. For JSON format, includes query, format, and api_version. For CSV, includes format, content_length, and query.</td><td style="word-wrap: break-word;">{   "query": "lung cancer",   "format": "json",   "api_version": "2.0" }</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **JSON is recommended**: Only the JSON format includes parsed fields like total_studies, min_rank, and max_rank in the structured output.
- **CSV returns raw text**: When using CSV format, the node returns the raw CSV content for results and a minimal JSON metadata string.
- **Query sensitivity**: The condition_or_disease string directly affects results; ensure accurate and specific terms for better counts.
- **Optional fields**: Providing a fields list can change response size and content; leave it empty if you only need counts.
- **Timeouts and errors**: Network calls use a 30-second timeout and return JSON strings with error messages on failures.

## Troubleshooting
- **Received an error JSON string (e.g., Network error)**: Check network connectivity, try again later, or reduce query complexity.
- **Unexpected empty or very low count**: Refine the condition_or_disease term, try synonyms, or confirm spelling and phrasing.
- **CSV output is hard to parse**: Switch format to json to get structured data with total_studies and related keys.
- **Large responses or slow performance**: Limit fields or use a more specific search term to reduce response size.
- **Invalid fields specified**: Remove or correct field names; invalid fields can lead to incomplete or confusing output.
