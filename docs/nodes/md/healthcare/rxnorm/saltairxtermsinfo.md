# RxTerms Information

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Fetches RxTerms metadata for one or more RxNorm concept IDs (RXCUIs). Returns JSON-encoded strings mapping each RXCUI to its full RxTerms response and key properties such as full name, strength, route, and dose forms. Supports single RXCUI or a JSON array of RXCUIs in the input.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/healthcare/rxnorm/saltairxtermsinfo.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need standardized medication term details from RxNorm's RxTerms for workflows like medication normalization, display, or downstream decision logic. Provide an RXCUI (or a JSON array string of RXCUIs) and consume the JSON outputs to populate UI fields, validate entries, or enrich records with route, strength, and dose form information.

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
<tr><td style="word-wrap: break-word;">rxcui</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">RxNorm Concept Unique Identifier. Accepts either a single RXCUI string (e.g., "313") or a JSON array string of RXCUIs (e.g., "[\"313\",\"1049630\"]").</td><td style="word-wrap: break-word;">["313","1049630"]</td></tr>
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
<tr><td style="word-wrap: break-word;">rxterms_info</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">JSON string mapping each RXCUI to its full RxTerms response object under rxtermsProperties and related fields.</td><td style="word-wrap: break-word;">{   "313": {     "rxtermsProperties": {       "fullName": "Example Drug 313 5 MG Oral Tablet",       "strength": "5 MG",       "route": "Oral",       "rxnormDoseForm": "Tablet"     }   } }</td></tr>
<tr><td style="word-wrap: break-word;">full_name</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">JSON string mapping RXCUI to the RxTerms fullName value.</td><td style="word-wrap: break-word;">{   "313": "Example Drug 313 5 MG Oral Tablet" }</td></tr>
<tr><td style="word-wrap: break-word;">strength</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">JSON string mapping RXCUI to the strength value.</td><td style="word-wrap: break-word;">{   "313": "5 MG" }</td></tr>
<tr><td style="word-wrap: break-word;">route</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">JSON string mapping RXCUI to the route of administration.</td><td style="word-wrap: break-word;">{   "313": "Oral" }</td></tr>
<tr><td style="word-wrap: break-word;">forms</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">JSON string mapping RXCUI to the RxNorm dose form (rxnormDoseForm).</td><td style="word-wrap: break-word;">{   "313": "Tablet" }</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Input flexibility**: The rxcui input can be a single ID (e.g., "313") or a JSON array string (e.g., "[\"313\",\"1049630\"]").
- **Output format**: All outputs are JSON strings keyed by RXCUI, even when a single RXCUI is provided.
- **Error handling**: If a given RXCUI is empty or an error occurs, that RXCUI is included with empty values; on unexpected exceptions the node returns "{}" for all outputs.
- **Field sources**: The 'forms' output corresponds to the rxtermsProperties.rxnormDoseForm field.
- **Whitespace sensitivity**: RXCUI strings are trimmed; blank entries result in empty outputs for that RXCUI.
- **Defaults**: Default rxcui input is "313" if none is provided.

## Troubleshooting
- **Empty outputs (all "{}")**: Indicates an exception occurred. Verify input format and network/API availability, then retry.
- **Missing keys for an RXCUI**: If a specific RXCUI shows empty strings, it may be invalid or not found; confirm the RXCUI and try again.
- **Invalid array input**: If using multiple RXCUIs, ensure the input is a valid JSON array string with quoted IDs (e.g., "[\"313\",\"1049630\"]").
- **Unexpected null or missing fields**: Some RxTerms properties may be absent for certain concepts; handle empty strings in downstream logic.
- **Rate limiting or connectivity issues**: Large lists or repeated calls may hit service limits; batch inputs or add delays and verify service status.
