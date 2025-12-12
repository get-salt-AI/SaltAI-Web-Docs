# ClinicalTrials Study Detail

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Retrieves detailed information for a single clinical study from ClinicalTrials.gov using its NCT ID. Supports selecting output format (JSON or CSV) and choosing how rich-text fields are returned (markdown or legacy). Optionally restricts returned fields for more focused responses.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/healthcare/clinicaltrials/clinicaltrialsstudydetailnode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need the full record for a known study by its NCT identifier (e.g., after a search step or when you already know the trial ID). Typical workflow: supply the NCT ID, choose the response format (JSON for structured processing or CSV for tabular workflows), optionally specify a comma-separated list of fields to limit the payload, and select the markup format for any rich-text fields.

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
<tr><td style="word-wrap: break-word;">nct_id</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The ClinicalTrials.gov NCT identifier for the study you want to retrieve.</td><td style="word-wrap: break-word;">NCT04000165</td></tr>
<tr><td style="word-wrap: break-word;">format</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Response format. JSON returns a JSON string; CSV returns text in CSV format.</td><td style="word-wrap: break-word;">json</td></tr>
<tr><td style="word-wrap: break-word;">markupFormat</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Controls how markup-type fields (e.g., descriptions) are returned. Choose 'markdown' for modern formatting or 'legacy' for older/plain formatting.</td><td style="word-wrap: break-word;">markdown</td></tr>
<tr><td style="word-wrap: break-word;">fields</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Optional comma-separated list of field names to include in the response. Leave empty to return default/full fields as defined by the API.</td><td style="word-wrap: break-word;">NCTId,BriefTitle,OverallStatus,Conditions,Locations</td></tr>
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
<tr><td style="word-wrap: break-word;">results</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The study detail payload as a string. If format=json, this is a JSON string; if format=csv, this is CSV text.</td><td style="word-wrap: break-word;">{"study": {"protocolSection": {"identificationModule": {"nctId": "NCT04000165"}}}}</td></tr>
<tr><td style="word-wrap: break-word;">metadata</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Supplementary metadata about the response, returned as a JSON string (e.g., nct_id, format, API version for JSON responses, or content length for CSV).</td><td style="word-wrap: break-word;">{"nct_id": "NCT04000165", "format": "json", "api_version": "2.0"}</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **NCT ID required**: You must provide a valid ClinicalTrials.gov NCT identifier (e.g., NCT01234567).
- **Output formats**: 'json' returns a JSON string suitable for parsing; 'csv' returns CSV text. Downstream nodes should handle the chosen format.
- **Markup behavior**: 'markupFormat' affects rich-text fields (e.g., descriptions). Use 'markdown' for formatted text or 'legacy' for backward-compatible plain formatting.
- **Field selection**: The 'fields' input must be a comma-separated list of ClinicalTrials.gov v2 field names. Supplying invalid or unknown fields may result in empty values or errors from the API.
- **Rate limits and availability**: Responses depend on ClinicalTrials.gov API availability and any applicable rate limits.
- **String outputs**: Both outputs are strings. If you need structured data, parse the 'results' JSON when format=json.

## Troubleshooting
- **Invalid NCT ID**: If 'results' contains an error or no study is found, verify the NCT ID spelling and existence on ClinicalTrials.gov.
- **Network errors**: If you see a network error in results/metadata, check your internet connection and retry later. Temporary API outages can also cause this.
- **JSON parsing downstream**: When format=json, ensure downstream steps parse the JSON string. If parsing fails, confirm the response isn't an error message.
- **Empty or unexpected fields**: If expected data is missing, remove or correct the 'fields' input to ensure valid field names are used.
- **CSV handling**: When format=csv, verify downstream tools expect CSV text. If not, switch to json format for structured parsing.
