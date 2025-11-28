# ClinicalTrials Study Fields

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Retrieves available field values from ClinicalTrials.gov for specified study fields. It queries the public API endpoint for field value statistics and returns the raw JSON results and a small metadata summary as strings. Handles common network and JSON parsing errors gracefully by returning error JSON strings.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/healthcare/clinicaltrials/clinicaltrialsstudyfieldsnode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need lists of values associated with specific ClinicalTrials.gov study fields (e.g., conditions, intervention names) to support downstream filtering, validation, or UI auto-complete. Typically placed early in a workflow to fetch canonical field values which can inform subsequent search or analysis nodes.

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
<tr><td style="word-wrap: break-word;">type</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Optional filter for field value types as supported by the ClinicalTrials.gov stats/field/values endpoint.</td><td style="word-wrap: break-word;">Study</td></tr>
<tr><td style="word-wrap: break-word;">fields</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Comma-separated list of study fields for which to retrieve values.</td><td style="word-wrap: break-word;">Condition, InterventionName</td></tr>
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
<tr><td style="word-wrap: break-word;">results</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Pretty-printed JSON string containing the API response with field values for the requested fields.</td><td style="word-wrap: break-word;">{ "FieldValues": [ { "Field": "Condition", "Values": ["Diabetes Mellitus", "Hypertension"] } ] }</td></tr>
<tr><td style="word-wrap: break-word;">metadata</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Pretty-printed JSON string with a minimal summary, including result_count.</td><td style="word-wrap: break-word;">{ "result_count": 2 }</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Network dependency**: Requires internet access to query ClinicalTrials.gov; a 30-second request timeout is enforced.
- **Error handling**: On failures, outputs are JSON strings indicating the error (e.g., {"error": "Network error"}).
- **Input flexibility**: All inputs are optional. If omitted, the node uses its default fields value.
- **Output format**: Both outputs are JSON strings (not objects). Downstream nodes expecting structured data should parse the strings.
- **Field names**: The accepted field names and type values must match what ClinicalTrials.gov supports for the stats/field/values endpoint.
- **Result count**: The metadata's result_count reflects the length of the top-level response structure returned by the API, which may vary by endpoint behavior.

## Troubleshooting
- **Network error**: If results contain {"error": "Network error"}, verify internet connectivity, firewall rules, and retry later.
- **JSON decode error**: If you see {"error": "JSON decode error"}, the API may have returned invalid or unexpected content; retry or adjust inputs.
- **Empty or unexpected results**: Confirm the field names in 'fields' are valid for the ClinicalTrials.gov stats/field/values endpoint.
- **Slow responses or timeouts**: Reduce requested fields, try again later, or check service status.
- **Downstream parsing issues**: Remember outputs are strings; ensure downstream nodes or scripts parse the JSON before use.
