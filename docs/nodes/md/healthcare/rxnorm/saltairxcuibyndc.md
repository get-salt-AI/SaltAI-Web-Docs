# RxCUI by NDC

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Looks up RxNorm Concept Unique Identifier(s) (RxCUI) for a given National Drug Code (NDC). It queries the RxNorm service and returns both the raw lookup payload and a parsed list of RxCUIs. Handles empty input validation and surfaces API errors in the status output.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/healthcare/rxnorm/saltairxcuibyndc.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you have an NDC and need to map it to RxNorm concept identifiers for downstream drug concept processing. Typical workflows feed the returned RxCUI list into nodes that fetch concept details, properties, related concepts, or interactions.

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
<tr><td style="word-wrap: break-word;">ndc</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The National Drug Code to look up. Provide a valid NDC as a string.</td><td style="word-wrap: break-word;">00071015527</td></tr>
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
<tr><td style="word-wrap: break-word;">rxcui_info</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">A JSON string containing the query NDC and the full response from the RxNorm lookup.</td><td style="word-wrap: break-word;">{"ndc": "00071015527", "rxcui_data": {"idGroup": {"rxnormId": ["12345"]}}}</td></tr>
<tr><td style="word-wrap: break-word;">rxcui</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">A JSON stringified array of RxCUI(s) extracted from the response. Empty array if none found.</td><td style="word-wrap: break-word;">["12345", "67890"]</td></tr>
<tr><td style="word-wrap: break-word;">status</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Operation status message indicating success or an error description.</td><td style="word-wrap: break-word;">Successfully retrieved RxCUI for NDC 00071015527</td></tr>
</tbody>
</table>
</div>

## Important Notes
- Input NDC must not be empty; otherwise the node returns an error status and empty outputs.
- The 'rxcui' output is a JSON array encoded as a string; parse it if you need a list structure downstream.
- If the RxNorm response does not include idGroup.rxnormId, the node returns an empty array for 'rxcui'.
- API errors are surfaced in the 'status' output as 'API Error: ...' and the detailed error payload is included in 'rxcui_info'.
- The node returns formatted JSON as strings; ensure consumers expecting objects parse these strings first.

## Troubleshooting
- Empty or missing NDC: Provide a non-empty NDC string; the node validates and will return 'Error: NDC cannot be empty'.
- API Error responses: Check the 'status' output for 'API Error: ...' and review 'rxcui_info' for the full error payload.
- No RxCUI returned: Verify the NDC is valid and active; some NDCs may not map to RxNorm or may have been retired.
- Unexpected output format: Remember that 'rxcui_info' and 'rxcui' are JSON strings; parse them before further processing.
