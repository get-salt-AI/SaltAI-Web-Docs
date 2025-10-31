# RxNorm Concept Info

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Retrieves detailed information for a specific RxNorm concept (RxCUI). You provide a JSON array of RxCUIs and select which one to inspect by index. Depending on the chosen info_type, it returns the concept's name, NDCs, core properties, or all related information as formatted JSON.

</div>
<div style="flex: 0 0 300px;"><img src="../../../images/previews/new-uncategorized/SaltAIRxNormConceptInfo.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node after obtaining one or more RxCUIs (for example, from a drug search or mapping by NDC). Pass the RxCUIs as a JSON array string, choose which RxCUI to inspect with selected_rxcui (0-based index), and select the type of information to retrieve. The node outputs the requested concept data in JSON, the selected RxCUI, and a status message for flow control and logging.

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
<tr><td style="word-wrap: break-word;">rxcuis</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">A JSON array string of RxCUIs to choose from. The node will parse this and select one by index.</td><td style="word-wrap: break-word;">["161", "16120", "198440"]</td></tr>
<tr><td style="word-wrap: break-word;">selected_rxcui</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">0-based index into the rxcuis array indicating which RxCUI to inspect.</td><td style="word-wrap: break-word;">0</td></tr>
<tr><td style="word-wrap: break-word;">info_type</td><td>True</td><td style="word-wrap: break-word;">["name", "ndcs", "properties", "all_related"]</td><td style="word-wrap: break-word;">Specifies which information to fetch for the selected RxCUI: name (RxNorm name), ndcs (linked National Drug Codes), properties (core concept properties), or all_related (comprehensive related info).</td><td style="word-wrap: break-word;">properties</td></tr>
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
<tr><td style="word-wrap: break-word;">concept_info</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Formatted JSON string containing the requested information for the selected RxCUI. Structure includes rxcui, info_type, and data.</td><td style="word-wrap: break-word;">{ "rxcui": "161", "info_type": "properties", "data": { ... } }</td></tr>
<tr><td style="word-wrap: break-word;">rxcui</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The RxCUI that was selected and queried.</td><td style="word-wrap: break-word;">161</td></tr>
<tr><td style="word-wrap: break-word;">status</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Human-readable status message indicating success or error details.</td><td style="word-wrap: break-word;">Successfully retrieved properties for RXCUI 161</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Input format**: rxcuis must be a valid JSON array string (e.g., "[\"161\"]"). Invalid JSON will cause an error.
- **Indexing**: selected_rxcui is 0-based. If the index is out of range, the node returns an error.
- **Empty values**: If the chosen RxCUI is an empty string, the node returns an error.
- **Info type options**: Only the listed values are accepted: name, ndcs, properties, all_related.
- **Error propagation**: If the upstream service returns an error, the node returns a JSON error payload in concept_info and an error message in status.
- **Output structure**: On success, concept_info wraps the raw data with rxcui and info_type for consistent downstream parsing.

## Troubleshooting
- **Selected RXCUI index out of range**: Ensure selected_rxcui is less than the length of the rxcuis array.
- **Invalid rxcuis JSON**: Provide a properly formatted JSON array string. Example: "[\"161\", \"198440\"]".
- **Empty RXCUI string**: Verify that the selected element in the rxcuis array is a non-empty string.
- **Unknown info_type**: Use only one of the supported values: name, ndcs, properties, all_related.
- **API Error in status**: Inspect concept_info for the error payload and retry later or adjust the input. Network or service issues may cause transient failures.
