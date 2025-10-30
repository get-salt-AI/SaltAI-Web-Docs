# ClinicalTrials Study Fields

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Retrieves available ClinicalTrials.gov study fields and their values via the API's field values endpoint. You can optionally filter by field type and select specific fields to include. Returns two JSON strings: the raw results and a small metadata object with a result count.

</div>
<div style="flex: 0 0 300px;"><img src="../../../images/previews/New-Uncategorized/ClinicalTrialsStudyFieldsNode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need to discover or enumerate study fields (for example, to understand which field names and values you can query against) before running searches or building downstream filtering. Typical workflow: run this node to list fields/values, inspect results and metadata, then feed chosen fields into study search or detail nodes.

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
<tr><td style="word-wrap: break-word;">type</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Optional filter for field categories/types provided by the ClinicalTrials.gov API.</td><td style="word-wrap: break-word;">Study, Location, Condition</td></tr>
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
<tr><td style="word-wrap: break-word;">results</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">JSON string containing the field values data returned by the API.</td><td style="word-wrap: break-word;">{ "Condition": ["Lung Cancer", "Diabetes"], "InterventionName": ["Drug A", "Procedure B"] }</td></tr>
<tr><td style="word-wrap: break-word;">metadata</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">JSON string with basic metadata about the response, including result_count.</td><td style="word-wrap: break-word;">{ "result_count": 2 }</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **API dependency**: Requires live access to ClinicalTrials.gov API v2; network issues will prevent retrieval.
- **Input formatting**: Supply multiple fields as a comma-separated string with proper field names recognized by the API.
- **Timeouts**: Requests use a 30-second timeout; slow networks or large responses may need retries.
- **Error handling**: On errors, outputs return JSON strings like {"error": "Network error"} instead of structured data.
- **Categories**: The node is grouped under SALT/ClinicalTrials/Study Fields for organization with related nodes.

## Troubleshooting
- **No results or empty lists**: Verify 'fields' and/or 'type' values are valid API-recognized names. Try removing filters to test connectivity.
- **Network error**: Check internet connectivity, firewall settings, or API availability; retry later.
- **JSON decode error**: Ensure the API is returning valid JSON; temporarily remove filters and test again.
- **Unexpected field names**: Consult ClinicalTrials.gov documentation for valid field identifiers or first call without 'type' to explore available options.
- **Slow or timed-out requests**: Reduce the number of requested fields, ensure stable connectivity, and retry.
