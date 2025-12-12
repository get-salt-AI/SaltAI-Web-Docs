# RxCUI by NDC

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Looks up RxNorm Concept Unique Identifiers (RxCUIs) for a given National Drug Code (NDC). It returns a structured JSON payload with the API response and a separate JSON array of extracted RxCUI IDs, along with a status message.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/healthcare/rxnorm/saltairxcuibyndc.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you have a drug's NDC and need to translate it to RxNorm identifiers for downstream clinical or analytics tasks. Typical workflow: provide the NDC, get back the RxCUI list, then pass those RxCUIs to nodes that fetch concept details, related concepts, or interactions.

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
<tr><td style="word-wrap: break-word;">ndc</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The National Drug Code to look up. Provide a valid NDC for the packaged product.</td><td style="word-wrap: break-word;">00071015527</td></tr>
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
<tr><td style="word-wrap: break-word;">rxcui_info</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Formatted JSON string containing the lookup result with the input NDC and the raw response payload.</td><td style="word-wrap: break-word;">{   "ndc": "00071015527",   "rxcui_data": { "idGroup": { "rxnormId": ["12345", "67890"] } } }</td></tr>
<tr><td style="word-wrap: break-word;">rxcui</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">JSON array string of extracted RxCUI identifiers from the response for easy chaining.</td><td style="word-wrap: break-word;">["12345", "67890"]</td></tr>
<tr><td style="word-wrap: break-word;">status</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Human-readable status of the operation, including success or error information.</td><td style="word-wrap: break-word;">Successfully retrieved RxCUI for NDC 00071015527</td></tr>
</tbody>
</table>
</div>

## Important Notes
- Provide the NDC as a numeric string. If your data includes hyphens, remove them for best compatibility.
- Outputs are JSON-encoded strings. Parse them if you need to access fields programmatically downstream.
- If the NDC is invalid or not found, the rxcui output may be an empty array and rxcui_info may still be returned with an empty or minimal idGroup.
- On input validation errors (e.g., empty NDC), the node returns an empty object string for rxcui_info, an empty string for rxcui, and a status message describing the error.
- External API rate limits or outages can affect results; retry or throttle if necessary.

## Troubleshooting
- Empty or missing NDC: Ensure the ndc input is provided and not just whitespace. The node will otherwise return an error status.
- No RxCUI returned: Verify the NDC is correct and corresponds to an active or known product. Try removing hyphens or using the 11-digit normalized NDC.
- Unexpected output format: Remember outputs are strings containing JSON. Parse them in subsequent steps before field access.
- API error in status: This indicates a remote lookup issue. Check connectivity, try again later, or validate the NDC against another source.
