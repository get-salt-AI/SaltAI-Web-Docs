# ClinicalTrials Study Fields

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Retrieves study field information from the ClinicalTrials.gov API and returns the results along with simple metadata. You can optionally filter by field types and specify a comma-separated list of fields to include.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/healthcare/clinicaltrials/clinicaltrialsstudyfieldsnode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you want to discover or retrieve available study fields and their values from ClinicalTrials.gov for analytics, data exploration, or to drive downstream filtering in a workflow. Typically placed early in a pipeline to fetch field/value information that informs later query or selection steps.

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
<tr><td style="word-wrap: break-word;">type</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Optional filter to limit returned field information by a specific field type.</td><td style="word-wrap: break-word;">Study</td></tr>
<tr><td style="word-wrap: break-word;">fields</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Comma-separated list of fields to include in the results.</td><td style="word-wrap: break-word;">Condition, InterventionName</td></tr>
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
<tr><td style="word-wrap: break-word;">results</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The API response as a JSON-formatted string containing study field information.</td><td style="word-wrap: break-word;">{"FieldValues": [...]}</td></tr>
<tr><td style="word-wrap: break-word;">metadata</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">A JSON-formatted string with summary metadata about the response (e.g., a simple result_count).</td><td style="word-wrap: break-word;">{"result_count": 42}</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Outputs are JSON strings**: Both outputs are JSON-encoded strings; parse them if you need structured data.
- **Optional inputs**: If no inputs are provided, the node requests general study field information and uses internal defaults for fields.
- **Field list format**: The 'fields' input must be a comma-separated list (spaces are allowed).
- **Network dependency**: This node makes an external HTTP request to the ClinicalTrials.gov API and requires internet access.
- **Error handling**: On errors, both outputs return JSON strings with an "error" key (e.g., {"error": "Network error"}).
- **Result count meaning**: The metadata result_count reflects the number of top-level items in the returned payload from the API.

## Troubleshooting
- **Empty or unexpected results**: Verify the 'type' and 'fields' values are valid for ClinicalTrials.gov. Try removing filters to see default behavior.
- **Network error**: Ensure internet connectivity and that the ClinicalTrials.gov API is reachable. The outputs will include {"error": "Network error"} on failure.
- **JSON decode error**: If the API response cannot be parsed, the node returns {"error": "JSON decode error"}. Retry later or adjust inputs.
- **Timeouts or slow responses**: The request uses a finite timeout. If timeouts occur, try again later or reduce filters to limit response size.
- **Unexpected metadata count**: result_count is derived from the top-level response structure. Inspect the 'results' JSON to understand the payload shape.
