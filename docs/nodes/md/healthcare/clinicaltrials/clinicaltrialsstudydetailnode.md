# ClinicalTrials Study Detail

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Retrieves detailed information about a single clinical study from ClinicalTrials.gov using its NCT ID. Supports JSON or CSV response formats and lets you choose how markup-type fields are returned (markdown or legacy). Returns both the study data and a small metadata summary.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/healthcare/clinicaltrials/clinicaltrialsstudydetailnode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need the full record for a known clinical study (for example, to display a study page, analyze study attributes, or enrich downstream processing). Provide the NCT ID and select the response and markup formats. Optionally restrict the payload to specific fields to reduce size.

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
<tr><td style="word-wrap: break-word;">nct_id</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The NCT identifier of the target study to retrieve. Must be a valid ClinicalTrials.gov NCT ID.</td><td style="word-wrap: break-word;">NCT04000165</td></tr>
<tr><td style="word-wrap: break-word;">format</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Response format of the study detail. Choose 'json' for structured JSON or 'csv' for comma-separated output.</td><td style="word-wrap: break-word;">json</td></tr>
<tr><td style="word-wrap: break-word;">markupFormat</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Presentation format for markup-type fields in the response. 'markdown' returns modern Markdown; 'legacy' returns legacy formatting.</td><td style="word-wrap: break-word;">markdown</td></tr>
<tr><td style="word-wrap: break-word;">fields</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Optional comma-separated list of fields to include in the response. Leave blank to return the full record.</td><td style="word-wrap: break-word;">BriefTitle,StudyType,Condition,InterventionName,Eligibility</td></tr>
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
<tr><td style="word-wrap: break-word;">results</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The study detail payload from ClinicalTrials.gov in the chosen format. For 'json', this is a JSON string; for 'csv', this is CSV text.</td><td style="word-wrap: break-word;">{ "studies": [ { "protocolSection": { } } ], "apiVersion": "2.0" }</td></tr>
<tr><td style="word-wrap: break-word;">metadata</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">A small JSON string summarizing the request and response, for example NCT ID, format, and either API version (for JSON) or content length (for CSV).</td><td style="word-wrap: break-word;">{ "nct_id": "NCT04000165", "format": "json", "api_version": "2.0" }</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **API endpoint**: Queries ClinicalTrials.gov API v2 at /studies/{nct_id}.
- **Formats**: 'json' returns structured JSON; 'csv' returns plain-text CSV. The 'markupFormat' option affects how rich-text fields are delivered.
- **Field filtering**: Use 'fields' to limit the response to specific fields and reduce payload size. Leave empty for the full record.
- **Timeout and errors**: Network errors or JSON parsing failures return error strings in both outputs (for example, {"error":"Network error"}).
- **Metadata differences**: For JSON responses, metadata includes api_version; for CSV responses, metadata includes content_length.
- **Case sensitivity**: Provide the NCT ID exactly as issued (for example, uppercase prefix 'NCT').

## Troubleshooting
- **Empty or invalid NCT ID**: Ensure 'nct_id' is a valid ClinicalTrials.gov NCT identifier (for example, NCT followed by digits).
- **Unexpected empty results**: Verify the study exists and is accessible; confirm there are no typos in the NCT ID.
- **Error: Network error**: Check network connectivity and retry. If the API is rate-limiting or down, wait and try again.
- **Error: JSON decode error**: Use 'format' = 'json' only if you expect JSON. If you requested 'csv', do not attempt to parse as JSON downstream.
- **Missing fields in response**: If you specified 'fields', make sure the field names match ClinicalTrials.gov field names exactly or remove 'fields' to return the full record.
- **Markup rendering issues**: Switch 'markupFormat' between 'markdown' and 'legacy' to align with your downstream renderer.
