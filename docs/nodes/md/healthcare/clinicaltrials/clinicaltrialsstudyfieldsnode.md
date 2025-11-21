# ClinicalTrials Study Fields

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Retrieves study field information from the ClinicalTrials.gov API, returning the raw results and lightweight metadata as JSON strings. You can optionally filter by field type and specify a comma-separated list of fields to include. Useful for discovering available fields and their values to inform downstream queries or UI options.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/healthcare/clinicaltrials/clinicaltrialsstudyfieldsnode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need to explore or enumerate study fields from ClinicalTrials.gov before building more targeted queries. Typically placed early in a workflow to fetch the list of fields (optionally filtered) and pass the results to parsers, selectors, or query-construction nodes.

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
<tr><td style="word-wrap: break-word;">type</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Optional filter for field types (as defined by the ClinicalTrials.gov API). Leave empty to return all applicable field information.</td><td style="word-wrap: break-word;">Study</td></tr>
<tr><td style="word-wrap: break-word;">fields</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Comma-separated list of field names to include in the results. If omitted, the API may return broader field information.</td><td style="word-wrap: break-word;">Condition, InterventionName</td></tr>
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
<tr><td style="word-wrap: break-word;">results</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">JSON string containing the API response with study fields information based on the provided filters.</td><td style="word-wrap: break-word;">{ "fields": [{ "name": "Condition", "values": [...] }, ... ] }</td></tr>
<tr><td style="word-wrap: break-word;">metadata</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">JSON string with basic metadata about the response. Includes result_count indicating the number of top-level items returned.</td><td style="word-wrap: break-word;">{ "result_count": 2 }</td></tr>
</tbody>
</table>
</div>

## Important Notes
- This node performs an external HTTP request to the ClinicalTrials.gov API; network connectivity is required.
- Inputs are optional and should be plain text. The 'fields' input must be a comma-separated list without special formatting.
- On errors, both outputs return JSON strings with an 'error' field (for example, {"error": "Network error"}).
- Response size and structure depend on the ClinicalTrials.gov API; downstream nodes should parse the 'results' JSON string accordingly.
- Default 'fields' is "Condition, InterventionName" if not overridden.

## Troubleshooting
- Network error: Ensure internet access and that the ClinicalTrials.gov API is reachable. Retry or reduce request frequency.
- JSON decode error: The upstream API response may be malformed or unexpected. Try again later or adjust inputs.
- Empty or unexpected results: Verify 'type' and 'fields' values match valid API field names; try removing filters to test.
- Slow responses or timeouts: The node uses a 30-second timeout. Narrow filters or try at a different time if the API is slow.
- Downstream parsing failures: Confirm you are parsing the 'results' output as JSON text before accessing fields.
