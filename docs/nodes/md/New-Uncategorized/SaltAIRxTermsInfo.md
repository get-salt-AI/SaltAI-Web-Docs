# RxTerms Information

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Retrieves RxTerms metadata for one or more RxNorm Concept Unique Identifiers (RXCUIs). It returns the full RxTerms response and key extracted fields (full name, strength, route, and dose forms) as JSON strings. Supports single RXCUI or a JSON array of RXCUIs in one request.

</div>
<div style="flex: 0 0 300px;"><img src="../../../images/previews/New-Uncategorized/SaltAIRxTermsInfo.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need standardized RxTerms information for medications identified by RxCUI(s). Typical workflow: provide an RXCUI (or a JSON array of RXCUIs) from an upstream lookup, then route the outputs to downstream nodes that parse or display drug full names, strengths, routes, and dose forms.

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
<tr><td style="word-wrap: break-word;">rxcui</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">An RxNorm Concept Unique Identifier. Accepts either a single RXCUI (e.g., "313") or a JSON array string of RXCUIs (e.g., "[\"313\", \"1049630\"]").</td><td style="word-wrap: break-word;">313</td></tr>
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
<tr><td style="word-wrap: break-word;">rxterms_info</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">JSON string mapping each input RXCUI to the full RxTerms response object.</td><td style="word-wrap: break-word;">{   "313": {     "rxtermsProperties": {       "fullName": "Aspirin 325 MG Oral Tablet",       "strength": "325 MG",       "route": "Oral",       "rxnormDoseForm": "Oral Tablet"     }   } }</td></tr>
<tr><td style="word-wrap: break-word;">full_name</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">JSON string mapping each RXCUI to its RxTerms full name.</td><td style="word-wrap: break-word;">{   "313": "Aspirin 325 MG Oral Tablet" }</td></tr>
<tr><td style="word-wrap: break-word;">strength</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">JSON string mapping each RXCUI to the strength value from RxTerms.</td><td style="word-wrap: break-word;">{   "313": "325 MG" }</td></tr>
<tr><td style="word-wrap: break-word;">route</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">JSON string mapping each RXCUI to the route of administration.</td><td style="word-wrap: break-word;">{   "313": "Oral" }</td></tr>
<tr><td style="word-wrap: break-word;">forms</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">JSON string mapping each RXCUI to the RxNorm dose form.</td><td style="word-wrap: break-word;">{   "313": "Oral Tablet" }</td></tr>
</tbody>
</table>
</div>

## Important Notes
- All outputs are JSON strings. Parse them downstream if you need structured access.
- Input can be either a single RXCUI or a JSON array string of RXCUIs (e.g., "[\"313\", \"1049630\"]").
- On lookup errors or invalid inputs, the node returns empty objects ("{}") for affected entries and does not raise an exception.
- Whitespace-only RXCUI entries are treated as errors and yield empty values.
- This node relies on an external RxNorm/RxTerms service via an internal API helper; availability and response quality depend on that service.

## Troubleshooting
- If all outputs are "{}": verify the RXCUI value is valid and correctly formatted.
- If using multiple RXCUIs, ensure the input is a valid JSON array string (e.g., "[\"313\", \"1049630\"]").
- If specific RXCUIs are missing in outputs: they may be invalid or the service returned an error for those entries.
- If network or service issues occur, retry later; intermittent failures result in empty outputs.
- If you expect numbers but provided them without quotes in the JSON array, ensure they are valid JSON (numbers are allowed, but strings are recommended to preserve formatting).
