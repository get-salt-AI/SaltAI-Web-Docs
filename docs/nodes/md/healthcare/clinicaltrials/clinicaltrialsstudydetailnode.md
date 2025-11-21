# ClinicalTrials Study Detail

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Retrieves detailed information for a single clinical study from ClinicalTrials.gov using its NCT ID. Supports JSON or CSV output and allows selecting specific fields and markup formatting for rich-text fields.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/healthcare/clinicaltrials/clinicaltrialsstudydetailnode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need the full record for a specific trial after you have an NCT ID (e.g., from a search step) or when you already know the study identifier. Typically placed after a study search or list node to fetch full details for a chosen study and then route the result to parsing, analysis, or display steps.

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
<tr><td style="word-wrap: break-word;">format</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Response format. Choose 'json' for structured JSON, or 'csv' for a CSV text response.</td><td style="word-wrap: break-word;">json</td></tr>
<tr><td style="word-wrap: break-word;">markupFormat</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Markup format for any rich-text fields returned by the API. 'markdown' or 'legacy'.</td><td style="word-wrap: break-word;">markdown</td></tr>
<tr><td style="word-wrap: break-word;">fields</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Optional comma-separated list of fields to include in the response. Leaving empty returns the default set.</td><td style="word-wrap: break-word;">BriefTitle, OverallStatus, Conditions, EnrollmentCount</td></tr>
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
<tr><td style="word-wrap: break-word;">results</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The study details returned by the ClinicalTrials.gov API. JSON string when format='json'; CSV text when format='csv'.</td><td style="word-wrap: break-word;">{   "apiVersion": "2.0",   "studies": [ { "protocolSection": { "identificationModule": { "nctId": "NCT04000165", "briefTitle": "..." } } } ] }</td></tr>
<tr><td style="word-wrap: break-word;">metadata</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Metadata about the response, including the NCT ID and format. For JSON responses, may include the API version; for CSV, includes content length.</td><td style="word-wrap: break-word;">{   "nct_id": "NCT04000165",   "format": "json",   "api_version": "2.0" }</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Endpoint behavior**: Queries ClinicalTrials.gov API v2 at /studies/{nct_id}.
- **Output types**: Results are always returned as strings. JSON format returns a JSON string; CSV returns raw CSV text.
- **Field selection**: Use the 'fields' parameter to limit payload size and focus on specific attributes.
- **Markup control**: 'markupFormat' applies to rich-text fields; choose 'markdown' for modern formatting or 'legacy' for older style.
- **Error handling**: On network/decoding errors, the node returns simple JSON error strings in both outputs rather than raising exceptions.
- **NCT ID validity**: An invalid or non-existent NCT ID may return an error from the API or an empty payload.
- **Rate limits/latency**: External API calls may be rate-limited or slow; plan for retries or backoff upstream if needed.

## Troubleshooting
- **Empty or unexpected results**: Verify the NCT ID is correct and active. If using 'fields', ensure the field names are valid; try removing 'fields' to compare.
- **CSV parsing issues**: If format='csv', downstream steps must parse CSV text. Switch to 'json' for structured parsing.
- **Markup rendering problems**: If rich-text fields display oddly, switch 'markupFormat' between 'markdown' and 'legacy' to match your renderer.
- **Network errors or timeouts**: Check connectivity and try again. The node returns an error JSON string; implement retry logic upstream if necessary.
- **Large responses**: If the output is too large to handle, restrict 'fields' to only what you need.
- **Invalid fields parameter**: If specific fields are not recognized by the API, they may be ignored or cause minimal data; validate field names against ClinicalTrials.gov documentation.
