# RxNorm Concept Info

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Retrieves detailed information about a specific RxNorm concept (RxCUI). You provide a JSON array of RxCUIs and choose which index to use, then select the type of information to fetch such as name, NDCs, properties, or all related data. Returns a formatted JSON payload, the selected RxCUI, and a status message.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/healthcare/rxnorm/saltairxnormconceptinfo.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node after obtaining one or more RxCUIs (e.g., from a drug search or NDC lookup). Pass the list of RxCUIs as a JSON array string, choose the index of the RxCUI you want to inspect, and select the information type. Integrate it into workflows that require drilling into a drug concept for metadata, identifiers, and related concepts.

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
<tr><td style="word-wrap: break-word;">rxcuis</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">JSON array string of RxCUIs to select from. The node will pick one RxCUI by index.</td><td style="word-wrap: break-word;">["161", "313", "860975"]</td></tr>
<tr><td style="word-wrap: break-word;">selected_rxcui</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Zero-based index into the rxcuis array indicating which RxCUI to query.</td><td style="word-wrap: break-word;">0</td></tr>
<tr><td style="word-wrap: break-word;">info_type</td><td>True</td><td style="word-wrap: break-word;">['name', 'ndcs', 'properties', 'all_related']</td><td style="word-wrap: break-word;">Selects the type of information to retrieve for the chosen RxCUI.</td><td style="word-wrap: break-word;">properties</td></tr>
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
<tr><td style="word-wrap: break-word;">concept_info</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Formatted JSON string containing the requested information about the selected RxCUI, including the RxCUI, the info_type, and the returned data.</td><td style="word-wrap: break-word;">{   "rxcui": "161",   "info_type": "properties",   "data": { ... } }</td></tr>
<tr><td style="word-wrap: break-word;">rxcui</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The selected RxCUI that was queried based on the provided index.</td><td style="word-wrap: break-word;">161</td></tr>
<tr><td style="word-wrap: break-word;">status</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Human-readable status indicating success or describing the encountered error.</td><td style="word-wrap: break-word;">Successfully retrieved properties for RXCUI 161</td></tr>
</tbody>
</table>
</div>

## Important Notes
- Input rxcuis must be a valid JSON array string. If it is not valid JSON or is empty, the node will return an error.
- selected_rxcui must be within the bounds of the rxcuis array; otherwise, an index out-of-range error is returned.
- info_type must be one of: name, ndcs, properties, all_related. Any other value will return an error.
- On API failures, the concept_info output will contain a JSON object including an error field, and status will describe the issue.
- Whitespace-only or empty RxCUI values are rejected.

## Troubleshooting
- Error: Selected RXCUI index out of range — Ensure selected_rxcui is >= 0 and less than the length of the rxcuis array.
- Error: RXCUI cannot be empty — Verify the rxcuis array contains non-empty string values and parsing succeeded.
- Error: Unknown info type — Set info_type to one of: name, ndcs, properties, all_related.
- API Error in outputs — The external service may be unavailable or the RxCUI is invalid. Inspect the concept_info JSON for the error field and retry with a known-good RxCUI.
- Invalid JSON for rxcuis — Provide a properly formatted JSON array string such as "[\"161\"]".
