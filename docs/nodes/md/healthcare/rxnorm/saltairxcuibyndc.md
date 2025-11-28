# RxCUI by NDC

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Looks up RxNorm Concept Unique Identifier(s) (RxCUI) for a given National Drug Code (NDC). It queries the RxNorm service and returns a structured JSON payload with the lookup details and a separate JSON array of the matching RxCUI values. Provides human-readable status messages for success and error cases.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/healthcare/rxnorm/saltairxcuibyndc.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you have an NDC and need to map it to RxNorm concepts for downstream medication analysis, normalization, or interoperability tasks. Typical workflows include converting package-level identifiers to RxCUIs prior to retrieving concept properties, related concepts, or performing drug class analytics.

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
<tr><td style="word-wrap: break-word;">ndc</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The National Drug Code (NDC) to resolve. Accepts 10- or 11-digit formats as a plain string. Preserve leading zeros.</td><td style="word-wrap: break-word;">00071015527</td></tr>
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
<tr><td style="word-wrap: break-word;">rxcui_info</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">A formatted JSON string containing the original NDC and the raw RxNorm response payload for that NDC.</td><td style="word-wrap: break-word;">{ "ndc": "00071015527", "rxcui_data": { "idGroup": { "rxnormId": ["161"] } } }</td></tr>
<tr><td style="word-wrap: break-word;">rxcui</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">A JSON string of an array containing the RxCUI(s) extracted from the response.</td><td style="word-wrap: break-word;">["161"]</td></tr>
<tr><td style="word-wrap: break-word;">status</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">A human-readable status message indicating success or detailing any error encountered.</td><td style="word-wrap: break-word;">Successfully retrieved RxCUI for NDC 00071015527</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Input format**: NDC must be provided as a string to preserve leading zeros; avoid numeric types.
- **Output formatting**: The 'rxcui' output is a JSON-encoded array string (e.g., "[\"161\"]"); parse it before use if your downstream step expects an array.
- **Empty or invalid NDC**: If the input is empty or invalid, the node returns "{}" for rxcui_info, an empty string for rxcui, and a status message describing the error.
- **API responses**: If the RxNorm service returns an error, the node forwards the error message in the status and rxcui_info while leaving 'rxcui' empty.
- **Multiple matches**: Some NDCs may map to multiple RxCUIs; expect multiple IDs in the 'rxcui' array.
- **No secrets required**: This node does not require tokens or secrets; do not supply any credentials.

## Troubleshooting
- **No RxCUI returned**: Verify the NDC is valid, correctly formatted as a string, and includes any leading zeros.
- **Parse errors downstream**: Ensure you parse the JSON string in the 'rxcui' field into an array before iterating or indexing.
- **Unexpected response structure**: Check 'rxcui_info' for the full raw payload to understand the returned structure and adjust parsing accordingly.
- **API error status**: If status begins with 'API Error', retry later or validate the NDC; intermittent service issues can occur.
- **Out-of-range or blank input**: Provide a non-empty NDC; the node returns an explicit error when the input is blank.
