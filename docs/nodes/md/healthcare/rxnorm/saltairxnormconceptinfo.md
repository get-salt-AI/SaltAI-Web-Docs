# RxNorm Concept Info

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Retrieves detailed information for a specific RxNorm concept. You provide a JSON string of RxCUI(s), choose one by index, and select the type of information to fetch (name, NDCs, concept properties, or all related info). Returns a formatted JSON payload with the requested data, the selected RxCUI, and a status message.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/healthcare/rxnorm/saltairxnormconceptinfo.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node after obtaining a list of RxCUIs from a prior step (e.g., a drug search). Pass the RxCUIs as a JSON string array, select which RxCUI to examine via its 0-based index, and choose the info type you need for downstream processing or display.

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
<tr><td style="word-wrap: break-word;">rxcuis</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">A JSON string representing an array of RxCUIs to choose from. The node will parse this string and use the index to select one RxCUI.</td><td style="word-wrap: break-word;">["161", "198440", "1000004"]</td></tr>
<tr><td style="word-wrap: break-word;">selected_rxcui</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">0-based index into the parsed RxCUI list indicating which RxCUI to fetch information for.</td><td style="word-wrap: break-word;">0</td></tr>
<tr><td style="word-wrap: break-word;">info_type</td><td>True</td><td style="word-wrap: break-word;">["name", "ndcs", "properties", "all_related"]</td><td style="word-wrap: break-word;">The category of information to retrieve for the selected RxCUI: 'name' (RxNorm display name), 'ndcs' (associated NDC codes), 'properties' (concept properties), or 'all_related' (all related concept information).</td><td style="word-wrap: break-word;">properties</td></tr>
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
<tr><td style="word-wrap: break-word;">concept_info</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">A prettified JSON string containing the selected RxCUI, the requested info_type, and the API response under 'data'.</td><td style="word-wrap: break-word;">{ "rxcui": "161", "info_type": "properties", "data": { ... } }</td></tr>
<tr><td style="word-wrap: break-word;">rxcui</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The selected RxCUI used to generate the result.</td><td style="word-wrap: break-word;">161</td></tr>
<tr><td style="word-wrap: break-word;">status</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Status message indicating success or describing an error condition.</td><td style="word-wrap: break-word;">Successfully retrieved properties for RXCUI 161</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Input format**: The rxcuis input must be a valid JSON array string (e.g., "[\"161\"]").
- **Indexing**: selected_rxcui is 0-based and must be within the bounds of the parsed rxcuis list.
- **Validation**: An empty or whitespace-only RxCUI will return an error.
- **Info types**: Only the values 'name', 'ndcs', 'properties', and 'all_related' are accepted.
- **Output JSON**: The concept_info output is a JSON string that includes keys 'rxcui', 'info_type', and 'data'.
- **API dependency**: This node depends on an external RxNorm service; network or service errors are surfaced in the status and concept_info fields.

## Troubleshooting
- **Selected RXCUI index out of range**: Ensure selected_rxcui is less than the length of the rxcuis array.
- **Invalid rxcuis JSON**: Provide a properly formatted JSON array string (e.g., "[\"161\", \"198440\"]").
- **Empty RxCUI**: Remove empty strings from the rxcuis array; the node rejects empty RxCUI values.
- **Unknown info type**: Use one of the allowed values: name, ndcs, properties, all_related.
- **API Error in status**: Retry later or verify the RxCUI is valid; network/service issues or invalid identifiers can cause API errors.
