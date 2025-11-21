# RxCUI by NDC

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Converts a National Drug Code (NDC) into its corresponding RxNorm Concept Unique Identifier(s) (RxCUI). It queries the RxNorm service and returns both the raw response and a convenient list of RxCUI IDs extracted from the result.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/healthcare/rxnorm/saltairxcuibyndc.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you have a medication's NDC and need the RxCUI to continue downstream tasks such as retrieving concept details, properties, or related concepts. Typical workflow: provide an NDC, get RxCUI(s), then feed the RxCUI(s) into other RxNorm nodes for further analysis.

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
<tr><td style="word-wrap: break-word;">ndc</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The National Drug Code for the medication to resolve. Must be a non-empty string.</td><td style="word-wrap: break-word;">00071015527</td></tr>
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
<tr><td style="word-wrap: break-word;">rxcui_info</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">A JSON string containing the input NDC and the full RxNorm response payload for that NDC.</td><td style="word-wrap: break-word;">{ "ndc": "00071015527", "rxcui_data": { "idGroup": { "rxnormId": ["161"] } } }</td></tr>
<tr><td style="word-wrap: break-word;">rxcui</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">A JSON array string of RxCUI IDs extracted from the response. Returns an empty array if none are found.</td><td style="word-wrap: break-word;">["161"]</td></tr>
<tr><td style="word-wrap: break-word;">status</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">A human-readable status message indicating success or describing an error.</td><td style="word-wrap: break-word;">Successfully retrieved RxCUI for NDC 00071015527</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Input validation**: The NDC must be non-empty; the node returns an error status if it is blank.
- **Output structure**: The 'rxcui' output is a JSON array string (not a native array). Parse it before iterating.
- **No-match behavior**: If the RxNorm response does not include any RxCUI IDs, the 'rxcui' output will be "[]".
- **API errors**: If the upstream service returns an error, the node forwards it in the 'rxcui_info' payload and sets a descriptive status.

## Troubleshooting
- **Empty input provided**: Ensure 'ndc' is a non-empty string. The node returns an error if it's blank.
- **No RxCUI returned**: Verify the NDC is valid and active. Some NDCs may not map to RxCUI or may be retired; the node will return an empty array.
- **API Error in status**: Check network connectivity and try again later. Inspect 'rxcui_info' for the forwarded error message.
- **Unexpected output format**: Remember that outputs are JSON strings. Parse them in downstream steps before accessing fields.
