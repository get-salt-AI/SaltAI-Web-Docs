# RxCUI by NDC

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Looks up RxNorm Concept Unique Identifiers (RxCUIs) using a National Drug Code (NDC). It queries the RxNorm service and returns the raw response plus a parsed list of RxCUIs, along with a status message. Designed to help bridge NDCs to their standardized RxNorm concepts.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/healthcare/rxnorm/saltairxcuibyndc.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you have an NDC and need to map it to one or more RxCUIs for downstream medication normalization, enrichment, or interoperability tasks. Commonly placed early in workflows that start from pharmacy data or claims and need standardized drug identifiers for subsequent analysis.

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
<tr><td style="word-wrap: break-word;">ndc</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The National Drug Code to resolve to RxCUI(s). Accepts a numeric string without separators.</td><td style="word-wrap: break-word;">00071015527</td></tr>
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
<tr><td style="word-wrap: break-word;">rxcui_info</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">JSON string containing the full API response enriched with the input NDC. Structure: {"ndc": "<input>", "rxcui_data": <RxNorm API result>}.</td><td style="word-wrap: break-word;">{"ndc": "00071015527", "rxcui_data": {"idGroup": {"rxnormId": ["12345","67890"]}}}</td></tr>
<tr><td style="word-wrap: break-word;">rxcui</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">JSON string array of RxCUI identifiers extracted from the response. Empty array if none found.</td><td style="word-wrap: break-word;">["12345","67890"]</td></tr>
<tr><td style="word-wrap: break-word;">status</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Human-readable status of the operation, including success or error context.</td><td style="word-wrap: break-word;">Successfully retrieved RxCUI for NDC 00071015527</td></tr>
</tbody>
</table>
</div>

## Important Notes
- Input validation: An empty or whitespace-only NDC returns an error status with empty outputs.
- Output formats: Both rxcui_info and rxcui are JSON strings. Parse them if you need native objects.
- Multiple matches: A single NDC may map to multiple RxCUIs; the rxcui output will contain an array.
- No matches: If no RxCUIs are found, rxcui will be an empty array ("[]").
- Error handling: Upstream API errors are surfaced in the status and the rxcui_info payload may include an error object.
- Connectivity: Requires network access to the RxNorm API. Transient failures may occur under rate limits or service interruptions.

## Troubleshooting
- Empty rxcui array: Verify the NDC format (no dashes/spaces) and ensure the code is active/valid.
- API Error in status: Retry after a short delay; check network connectivity and potential rate limiting.
- Unexpected JSON format: Inspect rxcui_info to see the exact API payload for structure changes or error details.
- Whitespace or blank input rejected: Ensure ndc is a non-empty string of digits.
