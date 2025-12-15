# ClinicalTrials Study Detail

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Retrieves detailed information for a specific clinical study from ClinicalTrials.gov using its NCT ID. Supports JSON or CSV output and optional control over markup fields and returned field selection. Returns both the raw result content and a small metadata summary.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/healthcare/clinicaltrials/clinicaltrialsstudydetailnode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you have a specific NCT ID and need the full detail record of that study. Typical workflows include validating search results, extracting protocol details, or generating reports. Choose JSON for structured data processing; select CSV if you need a flat textual representation.

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
<tr><td style="word-wrap: break-word;">nct_id</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The NCT identifier of the study to retrieve. Must be a valid ClinicalTrials.gov NCT ID.</td><td style="word-wrap: break-word;">NCT04000165</td></tr>
<tr><td style="word-wrap: break-word;">format</td><td>True</td><td style="word-wrap: break-word;">SELECT</td><td style="word-wrap: break-word;">Output format of the response content. JSON provides structured data; CSV provides a comma-separated text.</td><td style="word-wrap: break-word;">json</td></tr>
<tr><td style="word-wrap: break-word;">markupFormat</td><td>True</td><td style="word-wrap: break-word;">SELECT</td><td style="word-wrap: break-word;">Controls how markup-type fields are returned. Use 'markdown' for modern formatting or 'legacy' for older styles.</td><td style="word-wrap: break-word;">markdown</td></tr>
<tr><td style="word-wrap: break-word;">fields</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Optional, comma-separated list of specific fields to include in the response. Leave empty to return the default set.</td><td style="word-wrap: break-word;">NCTId,BriefTitle,OverallStatus,StudyType,Condition</td></tr>
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
<tr><td style="word-wrap: break-word;">results</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The retrieved study detail. When format=json, this is a JSON string; when format=csv, this is CSV text.</td><td style="word-wrap: break-word;">{   "apiVersion": "2.0",   "studies": [ ... ] }</td></tr>
<tr><td style="word-wrap: break-word;">metadata</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">A JSON string with basic metadata about the request/response, such as nct_id, format, apiVersion (for JSON), or content_length (for CSV).</td><td style="word-wrap: break-word;">{   "nct_id": "NCT04000165",   "format": "json",   "api_version": "2.0" }</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **NCT ID required**: You must supply a valid ClinicalTrials.gov NCT identifier (e.g., NCT01234567).
- **Output depends on format**: With format=json, `results` is a JSON string and metadata includes `api_version`. With format=csv, `results` is CSV text and metadata includes `content_length`.
- **Field filtering**: Use the `fields` input to limit the response to specific fields; provide them as a comma-separated list.
- **Markup handling**: `markupFormat` affects only markup-type fields within the JSON response; it has no effect when format=csv.
- **External API**: This node queries ClinicalTrials.gov API v2 and is subject to network availability and any API-side constraints.
- **Timeouts and errors**: Network timeouts are set; on error, both outputs may contain a JSON string with an `error` field.

## Troubleshooting
- **Invalid NCT ID**: Ensure the NCT ID is correctly formatted (e.g., NCT followed by digits). If the study is not found, the response may be empty or contain an error message.
- **Network error**: If you see '{"error": "Network error"}', check your internet connectivity or retry later; the API may be temporarily unavailable.
- **JSON decode error**: If you receive '{"error": "JSON decode error"}', switch to CSV or retry; this can occur if the API returns malformed JSON.
- **Unexpected empty fields**: If certain fields are missing, confirm your `fields` list is valid. Remove `fields` to get the default set and verify field names.
- **Large CSV output**: For CSV format, review `metadata.content_length` to gauge size. If too large, narrow the `fields` selection.
