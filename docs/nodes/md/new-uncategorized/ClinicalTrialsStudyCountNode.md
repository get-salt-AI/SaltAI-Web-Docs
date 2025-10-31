# ClinicalTrials Study Count

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Queries ClinicalTrials.gov (API v2) for the number of studies that match a given condition or disease. Returns a concise summary when JSON is selected, including total_studies and basic query metadata, or the raw text when CSV is selected.

</div>
<div style="flex: 0 0 300px;"><img src="../../../images/previews/new-uncategorized/ClinicalTrialsStudyCountNode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node to quickly gauge how many clinical studies exist for a particular condition before running more detailed searches or fetching study details. It fits early in a workflow to assess query scope, refine search terms, or decide whether to paginate or filter further.

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
<tr><td style="word-wrap: break-word;">condition_or_disease</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The search query string for a condition, disease, or term to match studies.</td><td style="word-wrap: break-word;">lung cancer</td></tr>
<tr><td style="word-wrap: break-word;">format</td><td>True</td><td style="word-wrap: break-word;">['json', 'csv']</td><td style="word-wrap: break-word;">The desired output format. JSON returns a structured summary with total_studies; CSV returns raw text from the API.</td><td style="word-wrap: break-word;">json</td></tr>
<tr><td style="word-wrap: break-word;">fields</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Comma-separated list of additional fields to include in the API response. Optional and typically unnecessary when only the count is needed.</td><td style="word-wrap: break-word;">NCTId,BriefTitle,OverallStatus</td></tr>
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
<tr><td style="word-wrap: break-word;">results</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">When format=json, a pretty-printed JSON string containing total_studies, query info, and raw API data. When format=csv, the raw CSV text from the API.</td><td style="word-wrap: break-word;">{   "data": { ... },   "query": "lung cancer",   "total_studies": 12456,   "min_rank": 1,   "max_rank": 1,   "api_version": "2.0" }</td></tr>
<tr><td style="word-wrap: break-word;">metadata</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">A JSON string with metadata about the request. For JSON output: includes query, format, and api_version. For CSV output: includes query, format, and content_length.</td><td style="word-wrap: break-word;">{   "query": "lung cancer",   "format": "json",   "api_version": "2.0" }</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **JSON vs CSV behavior**: JSON returns a structured summary including total_studies. CSV returns the raw response text; the count is not extracted for you.
- **Supported formats**: Inputs accept json or csv. The node internally handles non-JSON formats by returning raw text.
- **Search parameter**: The primary query parameter used is query.cond set to the value of condition_or_disease.
- **Optional fields**: Specifying fields can alter the API payload; it is not required to obtain the total study count.
- **Error handling**: On network or JSON parsing issues, the node returns a simple error JSON string in both outputs.

## Troubleshooting
- **No or low counts returned**: Refine condition_or_disease (use broader terms or synonyms) and try again.
- **Unexpected raw text in results**: Ensure format is set to json if you expect a parsed JSON summary with total_studies.
- **Timeouts or network errors**: Check internet connectivity and try again later; the service may be temporarily unavailable.
- **Invalid or empty fields**: Remove or correct the fields parameter if the API returns unexpected content, then retry with only the condition and format.
- **CSV content length is zero**: The API may have returned no rows for the query; verify the search term or switch to JSON for clearer diagnostics.
