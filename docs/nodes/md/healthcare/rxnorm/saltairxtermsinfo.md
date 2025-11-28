# RxTerms Information

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Retrieves RxTerms details for one or more RxNorm concept identifiers (RXCUIs). It returns JSON strings that map each RXCUI to full term info and selected fields: fullName, strength, route, and dose forms. Accepts either a single RXCUI or a JSON array string of RXCUIs.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/healthcare/rxnorm/saltairxtermsinfo.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need structured RxTerms metadata for medications identified by RXCUI. Typical workflows include normalizing drug information, enriching clinical data with standardized routes and strengths, or preparing downstream displays and matching logic. Provide one RXCUI (e.g., "313") or a JSON array string (e.g., "[\"313\", \"617314\"]").

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
<tr><td style="word-wrap: break-word;">rxcui</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">RxNorm Concept Unique Identifier. Supports a single RXCUI string or a JSON array string of RXCUIs. Whitespace-only values are treated as invalid.</td><td style="word-wrap: break-word;">313</td></tr>
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
<tr><td style="word-wrap: break-word;">rxterms_info</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Pretty-printed JSON string mapping each RXCUI to the full RxTerms response object for that concept.</td><td style="word-wrap: break-word;">{   "313": {     "rxtermsProperties": {       "fullName": "Acetaminophen 325 MG Oral Tablet",       "strength": "325 MG",       "route": "ORAL",       "rxnormDoseForm": "Oral Tablet"     }   } }</td></tr>
<tr><td style="word-wrap: break-word;">full_name</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Pretty-printed JSON string mapping each RXCUI to its RxTerms fullName.</td><td style="word-wrap: break-word;">{   "313": "Acetaminophen 325 MG Oral Tablet" }</td></tr>
<tr><td style="word-wrap: break-word;">strength</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Pretty-printed JSON string mapping each RXCUI to its strength value.</td><td style="word-wrap: break-word;">{   "313": "325 MG" }</td></tr>
<tr><td style="word-wrap: break-word;">route</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Pretty-printed JSON string mapping each RXCUI to its route of administration.</td><td style="word-wrap: break-word;">{   "313": "ORAL" }</td></tr>
<tr><td style="word-wrap: break-word;">forms</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Pretty-printed JSON string mapping each RXCUI to its dose form (rxnormDoseForm).</td><td style="word-wrap: break-word;">{   "313": "Oral Tablet" }</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Input flexibility**: The rxcui input may be a single RXCUI (e.g., "313") or a JSON array string (e.g., "[\"313\", \"617314\"]").
- **Per-RXCUI mapping**: All outputs are JSON strings where each top-level key is the input RXCUI; this is true even when only one value is provided.
- **Error handling (per RXCUI)**: If an RXCUI is invalid, empty, or returns an error, that RXCUI will be present in outputs with empty values (e.g., {} in rxterms_info and empty strings for other fields).
- **Global failure**: On unexpected exceptions, all five outputs are "{}" strings.
- **Network dependency**: Requires access to the RxNorm/RxTerms API; network or service issues can affect results.
- **Field names**: The main response includes rxtermsProperties with keys like fullName, strength, route, and rxnormDoseForm.

## Troubleshooting
- **Malformed list input**: If providing multiple RXCUIs, ensure the input is a valid JSON array string (e.g., "[\"313\", \"617314\"]"). Invalid JSON will result in all outputs being "{}".
- **Empty or whitespace RXCUI**: Whitespace-only entries are treated as invalid and yield empty values for that RXCUI.
- **Unknown RXCUI**: Nonexistent or deprecated RXCUIs may return empty values. Verify the RXCUI in the RxNorm database.
- **API/network errors**: Connectivity issues can cause all outputs to be "{}". Retry later or check network and service status.
- **Missing fields**: If strength, route, or dose form are unavailable for a given RXCUI, the corresponding output entry will be an empty string for that RXCUI.
- **Batch input issues**: When mixing valid and invalid RXCUIs in a list, valid ones will still return data; invalid ones will have empty entries.
