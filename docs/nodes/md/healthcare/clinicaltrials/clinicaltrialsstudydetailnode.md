# ClinicalTrials Study Detail

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Retrieves detailed information for a single clinical study from ClinicalTrials.gov using its NCT ID. You can choose the response format (JSON or CSV) and control how any markup fields are returned (markdown or legacy/plain). Optionally, you can request a subset of fields to reduce the payload.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/healthcare/clinicaltrials/clinicaltrialsstudydetailnode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you know a study's NCT ID and need its full or filtered details for downstream analysis or display. Typical workflow: supply the NCT ID, select output and markup formats, optionally specify a comma-separated list of fields, then pass the resulting string to parsing, rendering, or storage nodes.

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
<tr><td style="word-wrap: break-word;">nct_id</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The ClinicalTrials.gov NCT identifier of the study to retrieve.</td><td style="word-wrap: break-word;">NCT04000165</td></tr>
<tr><td style="word-wrap: break-word;">format</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Response format for the study detail. Choose 'json' for a JSON string or 'csv' for a CSV string.</td><td style="word-wrap: break-word;">json</td></tr>
<tr><td style="word-wrap: break-word;">markupFormat</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">How markup-type fields (e.g., descriptions) are returned. 'markdown' preserves markdown; 'legacy' returns simplified/plain formatting.</td><td style="word-wrap: break-word;">markdown</td></tr>
<tr><td style="word-wrap: break-word;">fields</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Optional comma-separated list of specific fields to include. Leave empty to return the default/full payload for the selected format.</td><td style="word-wrap: break-word;">NCTId,BriefTitle,OverallStatus,Conditions</td></tr>
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
<tr><td style="word-wrap: break-word;">results</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The study detail payload as a string in the selected format (JSON or CSV).</td><td style="word-wrap: break-word;">{"studies":[{"protocolSection":{"identificationModule":{"nctId":"NCT04000165"}}}]}</td></tr>
<tr><td style="word-wrap: break-word;">metadata</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Metadata about the request/response as a string (may include status or request info).</td><td style="word-wrap: break-word;">{"status":"ok","source":"clinicaltrials.gov"}</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **NCT ID required**: You must provide a valid NCT identifier (e.g., NCT########) for the study to be retrieved.
- **Format selection**: The node returns a string; if 'csv' is selected, the content is CSV-formatted text. If 'json' is selected, the content is a JSON string.
- **Markup handling**: 'markdown' preserves markdown formatting in applicable fields; 'legacy' returns simplified/plain text for compatibility.
- **Field filtering**: Providing the 'fields' list can significantly reduce payload size; ensure field names match ClinicalTrials.gov API field names.
- **External API dependency**: Results depend on ClinicalTrials.gov availability and network connectivity.

## Troubleshooting
- **Empty or error response**: Verify the 'nct_id' is correct and currently available on ClinicalTrials.gov. Invalid IDs may return empty results.
- **Unexpected formatting**: Check 'format' and 'markupFormat' inputs. If you expected JSON but received CSV (or vice versa), adjust 'format'.
- **Missing fields in output**: Ensure 'fields' are correctly spelled and supported by the ClinicalTrials.gov API. Remove or correct invalid field names.
- **Downstream parsing errors**: Remember outputs are strings. If a downstream node expects structured data, parse the JSON or CSV first.
- **Rate limit or timeout**: If requests fail intermittently, retry later and ensure network access to ClinicalTrials.gov.
