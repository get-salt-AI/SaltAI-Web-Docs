# RxNorm Concept Info

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Retrieves detailed information for a specific RxNorm concept (RxCUI). You provide a list of RxCUIs and choose which one to inspect by index, then select what kind of information to fetch (name, NDCs, concept properties, or all related info). Returns the requested info as JSON, alongside the selected RxCUI and a status message.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/healthcare/rxnorm/saltairxnormconceptinfo.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node after obtaining a list of RxCUIs (for example, from a drug search or NDC lookup). Pass the RxCUIs as a JSON array string, choose the index of the concept you want, and select the info type. Typical workflow: search for a drug → get RxCUIs → feed the list here → retrieve properties, NDC mappings, or related concepts for a specific RxCUI.

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
<tr><td style="word-wrap: break-word;">rxcuis</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">A JSON array string of RxCUIs to choose from. The node will parse this string, select one RxCUI by index, and fetch info for it.</td><td style="word-wrap: break-word;">["161", "10582", "198440"]</td></tr>
<tr><td style="word-wrap: break-word;">selected_rxcui</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Zero-based index into the rxcuis list specifying which RxCUI to query.</td><td style="word-wrap: break-word;">0</td></tr>
<tr><td style="word-wrap: break-word;">info_type</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The type of information to retrieve for the selected RxCUI. Options: "name" (RxNorm name), "ndcs" (associated National Drug Codes), "properties" (concept properties/metadata), or "all_related" (broad related concept information).</td><td style="word-wrap: break-word;">properties</td></tr>
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
<tr><td style="word-wrap: break-word;">concept_info</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">JSON string containing the requested data for the selected RxCUI, including the RxCUI, the info_type, and the returned payload.</td><td style="word-wrap: break-word;">{   "rxcui": "161",   "info_type": "properties",   "data": { /* properties payload from RxNorm */ } }</td></tr>
<tr><td style="word-wrap: break-word;">rxcui</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The selected RxCUI used for the lookup.</td><td style="word-wrap: break-word;">161</td></tr>
<tr><td style="word-wrap: break-word;">status</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Status message indicating success or the nature of any error.</td><td style="word-wrap: break-word;">Successfully retrieved properties for RXCUI 161</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Input format**: rxcuis must be a valid JSON array string (e.g., "[\"161\", \"10582\"]"). Plain strings like "161" will fail parsing.
- **Indexing**: selected_rxcui is zero-based and must be within the bounds of the rxcuis list.
- **Empty values**: If the chosen RxCUI is empty or whitespace, the node returns an error.
- **Info types**: The structure of the concept_info JSON varies by info_type. Choose the type that fits your downstream processing.
- **Error handling**: On API or parsing errors, concept_info returns "{}" and rxcui may be empty with an error status.
- **Chaining**: This node pairs well with nodes that return RxCUI lists (e.g., a search node). Feed their RxCUI list output directly into rxcuis.

## Troubleshooting
- **Error: Selected RXCUI index out of range**: Ensure selected_rxcui is less than the length of the provided rxcuis list.
- **Error: RXCUI cannot be empty**: Verify the selected entry in rxcuis is a non-empty string.
- **JSON parsing error for rxcuis**: Confirm rxcuis is a valid JSON array string (including quotes and brackets).
- **API Error: ...**: The upstream RxNorm service returned an error. Retry later, verify the RxCUI is valid, or switch info_type.
- **Error: Unknown info type**: Use only supported values: name, ndcs, properties, all_related.
