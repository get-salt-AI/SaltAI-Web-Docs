# RxTerms Information

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Retrieves RxTerms details for one or more RxNorm concepts (RXCUIs) using the RxNorm API. Accepts a single RXCUI or a JSON array of RXCUIs and returns JSON-encoded summaries including the full name, strength, route, and dose forms for each concept. Designed to provide quick, structured drug terminology data keyed by RXCUI.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/healthcare/rxnorm/saltairxtermsinfo.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need standardized drug terminology data from RxNorm RxTerms for downstream processing, display, or validation. Provide a single RXCUI for one concept, or pass a JSON array of RXCUIs to batch fetch details and receive consolidated JSON outputs keyed by each RXCUI.

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
<tr><td style="word-wrap: break-word;">rxcui</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">RxNorm Concept Unique Identifier. Can be a single RXCUI (e.g., "313") or a JSON array string of RXCUIs (e.g., "[\"313\", \"123\"]").</td><td style="word-wrap: break-word;">313</td></tr>
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
<tr><td style="word-wrap: break-word;">rxterms_info</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">JSON string mapping each RXCUI to the full RxTerms API response object for that concept.</td><td style="word-wrap: break-word;">{   "313": { "rxtermsProperties": { "fullName": "Acetaminophen 325 MG Oral Tablet", "strength": "325 mg", "route": "Oral", "rxnormDoseForm": "Oral Tablet" } } }</td></tr>
<tr><td style="word-wrap: break-word;">full_name</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">JSON string mapping each RXCUI to its RxTerms fullName.</td><td style="word-wrap: break-word;">{ "313": "Acetaminophen 325 MG Oral Tablet" }</td></tr>
<tr><td style="word-wrap: break-word;">strength</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">JSON string mapping each RXCUI to its strength value from RxTerms.</td><td style="word-wrap: break-word;">{ "313": "325 mg" }</td></tr>
<tr><td style="word-wrap: break-word;">route</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">JSON string mapping each RXCUI to its route of administration from RxTerms.</td><td style="word-wrap: break-word;">{ "313": "Oral" }</td></tr>
<tr><td style="word-wrap: break-word;">forms</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">JSON string mapping each RXCUI to its dose form (rxnormDoseForm) from RxTerms.</td><td style="word-wrap: break-word;">{ "313": "Oral Tablet" }</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Input format**: The input accepts either a single RXCUI string or a JSON array string (e.g., "[\"313\", \"123\"]").
- **Batch behavior**: When a JSON array is provided, all outputs are JSON objects keyed by each RXCUI.
- **Empty or invalid RXCUI**: Blank or invalid RXCUIs yield empty strings for the specific fields in the per-RXCUI mappings.
- **Error handling**: On unexpected errors, all outputs default to empty JSON objects ("{}").
- **External dependency**: Requires network access to the RxNorm API via the underlying service; connectivity or service issues can affect results.
- **Output format**: All outputs are JSON-encoded strings; parse them before programmatic use.

## Troubleshooting
- **All outputs are {}**: This indicates an exception occurred during retrieval. Check network connectivity, RXCUI validity, and service availability; try a single known-good RXCUI.
- **Fields are empty strings for some RXCUIs**: The specific RXCUI may be invalid or returned an error. Verify the RXCUI and retry; ensure there is no leading/trailing whitespace.
- **'rxcui' array not recognized**: Ensure you pass a valid JSON array string with properly escaped quotes (e.g., "[\"313\", \"123\"]").
- **Unexpected parsing issues**: Confirm that the input is either a plain RXCUI string or a valid JSON array string; avoid passing Python-style lists or malformed JSON.
- **Missing properties in output**: Some concepts may not have all RxTerms properties populated. Validate data presence before downstream usage.
