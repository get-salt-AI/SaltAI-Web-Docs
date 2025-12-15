# ClinicalTrials Study Fields

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Retrieves available study field values from ClinicalTrials.gov via the v2 API. You can optionally filter by field type and specify a comma-separated list of fields to include. Returns the raw results and a lightweight metadata summary as JSON strings.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/healthcare/clinicaltrials/clinicaltrialsstudyfieldsnode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node to explore or retrieve lists of values for study fields (e.g., conditions, intervention names) to support downstream filtering, validation, or UI population. Commonly placed before search or analytics nodes to determine which fields and values are available for a given context.

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
<tr><td style="word-wrap: break-word;">type</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Filter by field types. When provided, limits the returned values to the specified field type category.</td><td style="word-wrap: break-word;">Study</td></tr>
<tr><td style="word-wrap: break-word;">fields</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Comma-separated list of field names to include in the response.</td><td style="word-wrap: break-word;">Condition, InterventionName</td></tr>
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
<tr><td style="word-wrap: break-word;">results</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">JSON string containing the fetched field values from ClinicalTrials.gov.</td><td style="word-wrap: break-word;">{   "Condition": ["Diabetes Mellitus", "Hypertension", ...],   "InterventionName": ["Metformin", "Placebo", ...] }</td></tr>
<tr><td style="word-wrap: break-word;">metadata</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">JSON string containing a simple summary of the request, including result_count.</td><td style="word-wrap: break-word;">{   "result_count": 2 }</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Outputs are JSON strings**: You may need to parse the strings into objects before further processing.
- **Optional inputs**: Both 'type' and 'fields' are optional; leaving them empty returns broader results.
- **Network and parsing errors**: On errors, the node returns JSON strings with an "error" field for both outputs rather than raising exceptions.
- **Timeout**: The request uses a 30-second timeout; slow responses may cause a network error.
- **API endpoint**: Uses ClinicalTrials.gov v2 endpoint for field values.

## Troubleshooting
- **Empty or unexpected results**: Verify the 'fields' list contains valid field names (comma-separated, no extra spaces if possible). Remove 'type' to broaden the query.
- **Network error returned**: Check internet connectivity, VPN/proxy settings, and try again. The node returns '{"error": "Network error"}' in both outputs when the request fails or times out.
- **JSON decode error**: The API response could not be parsed. Retry later; if persistent, ensure inputs are valid and not causing a non-JSON response.
- **Incorrect filtering**: If results lack expected fields, ensure the 'fields' input is correctly spelled and comma-separated, e.g., "Condition, InterventionName".
