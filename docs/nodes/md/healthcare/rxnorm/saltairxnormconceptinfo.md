# RxNorm Concept Info

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Retrieves detailed information for a specific RxNorm concept (RxCUI). You supply a JSON array of RxCUIs and choose which index to inspect, then select the type of information to fetch (name, NDCs, properties, or all related data). Returns a JSON-formatted payload with the requested data, the selected RxCUI, and a status message.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/healthcare/rxnorm/saltairxnormconceptinfo.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node after you have a list of candidate RxCUIs (for example, from a drug search). Feed the JSON array of RxCUIs into this node, set the index of the RxCUI you want to examine, and pick the info_type that matches your needs. Typical workflow: Search for drugs → get list of RxCUIs → pass into this node to retrieve concept name, associated NDCs, core properties, or full related concept graph.

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
<tr><td style="word-wrap: break-word;">rxcuis</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">A JSON array of RxNorm Concept Unique Identifiers (RxCUIs) as strings. The node will parse this JSON and pick one RxCUI by index.</td><td style="word-wrap: break-word;">["161", "7052", "1234"]</td></tr>
<tr><td style="word-wrap: break-word;">selected_rxcui</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Zero-based index into the rxcuis array indicating which RxCUI to use.</td><td style="word-wrap: break-word;">0</td></tr>
<tr><td style="word-wrap: break-word;">info_type</td><td>True</td><td style="word-wrap: break-word;">CHOICE</td><td style="word-wrap: break-word;">Type of information to retrieve for the selected RxCUI. Options: name (concept name), ndcs (associated National Drug Codes), properties (concept properties), all_related (comprehensive related information).</td><td style="word-wrap: break-word;">properties</td></tr>
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
<tr><td style="word-wrap: break-word;">concept_info</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">JSON string containing the selected RxCUI, requested info_type, and the retrieved data payload.</td><td style="word-wrap: break-word;">{   "rxcui": "161",   "info_type": "properties",   "data": { "propConceptGroup": { "propConcept": [ ... ] } } }</td></tr>
<tr><td style="word-wrap: break-word;">rxcui</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The selected RxCUI from the input list that was used to fetch information.</td><td style="word-wrap: break-word;">161</td></tr>
<tr><td style="word-wrap: break-word;">status</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Operation status message indicating success or describing an error.</td><td style="word-wrap: break-word;">Successfully retrieved properties for RXCUI 161</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Input format**: rxcuis must be valid JSON representing an array of strings (e.g., ["161"]). Invalid JSON or non-array values will cause errors.
- **Indexing**: selected_rxcui is zero-based. If the index is outside the array bounds, the node returns an out-of-range error.
- **Empty values**: The chosen RxCUI string must be non-empty; otherwise the node returns an error.
- **info_type choice**: The node supports only: name, ndcs, properties, all_related. Any other value returns an error.
- **External API dependency**: Data is obtained from an RxNorm API via a utility layer; network or service issues may produce API error messages.
- **Payload size**: all_related can return large responses; consider performance and downstream node limits.

## Troubleshooting
- **Invalid JSON in rxcuis**: Ensure the input is a properly formatted JSON array of strings (e.g., "[\"161\"]").
- **Selected RXCUI index out of range**: Verify selected_rxcui is within 0 and len(rxcuis)-1.
- **Empty RXCUI**: Confirm the rxcuis array contains non-empty string values.
- **API Error responses**: The status may include messages like "API Error: ...". Retry later, validate the RxCUI, or check network connectivity.
- **Unexpected output shape**: For all_related, the structure can be deeply nested; pass the output through a JSON inspector or parser before further processing.
