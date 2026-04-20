# RxNorm Concept Info

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

This node calls the RxNorm service to fetch detailed information about a single drug concept identified by an RxCUI. It accepts a JSON-encoded list of RxCUIs, selects one entry by index, and retrieves either its name, associated NDCs, core properties, or a comprehensive related-information bundle. The node validates inputs, wraps the API response in a stable JSON envelope, and outputs both the data and a status string for easy downstream use.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/healthcare/rxnorm/saltairxnormconceptinfo.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you already have one or more RxCUIs and need rich information about one selected concept for display, normalization, or analysis. Common upstream nodes are SaltAIRxNormSearch (to find RxCUIs from drug names), SaltAIRxNormApproximateMatch (for fuzzy search), and SaltAIRxCUIByNDC (to resolve NDCs to RxCUIs); these typically produce or can be transformed into a JSON list of RxCUIs which you feed into the rxcuis input here. Control which concept is examined by adjusting selected_rxcui (0-based index into that list), and choose info_type based on your goal: use "name" when you need a canonical RxNorm label for UIs or deduplication, "ndcs" when mapping concepts to specific product codes, "properties" for semantic type and structural characteristics, and "all_related" for broader clinical or formulary exploration. Downstream, concept_info is usually parsed by custom logic, visualization/reporting components, or other Rx-focused nodes such as SaltAIRxNormRelatedConcepts or population analysis flows that aggregate multiple concepts. In iterative or UI-driven workflows, keep the rxcuis list constant and vary only selected_rxcui so users can step through candidates without re-running upstream search nodes.

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
<tr><td style="word-wrap: break-word;">rxcuis</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">JSON-encoded list of RxNorm Concept Unique Identifiers (RxCUIs) to choose from. The string is parsed with a JSON parser and must decode to a list (typically a list of strings such as ["161","7052"]). If the string is not valid JSON or does not represent a list, the node will fail internally and return an empty concept_info payload with an error status.</td><td style="word-wrap: break-word;">["161","7052","198440"]</td></tr>
<tr><td style="word-wrap: break-word;">selected_rxcui</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Zero-based index into the decoded rxcuis list indicating which RxCUI to query. Must be between 0 and len(rxcuis)-1. If the index is out of range, the node does not call the API and instead returns {} for concept_info, an empty rxcui output, and a status of "Error: Selected RXCUI index out of range".</td><td style="word-wrap: break-word;">0</td></tr>
<tr><td style="word-wrap: break-word;">info_type</td><td>True</td><td style="word-wrap: break-word;">["name","ndcs","properties","all_related"]</td><td style="word-wrap: break-word;">Specifies which type of information to retrieve for the selected RxCUI. "name" returns standardized RxNorm naming information; "ndcs" returns associated National Drug Codes; "properties" returns core concept properties such as semantic type or form where available; "all_related" returns a comprehensive bundle of related-concept information. Any value outside this set results in an error status and empty data.</td><td style="word-wrap: break-word;">"properties"</td></tr>
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
<tr><td style="word-wrap: break-word;">concept_info</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Pretty-printed JSON string containing the requested information for the selected RxCUI. The structure is an envelope of the form { "rxcui": "<id>", "info_type": "<type>", "data": <api_result> }, where data is the raw RxNorm API response for the chosen info type. This consistent wrapper simplifies parsing and downstream processing.</td><td style="word-wrap: break-word;">{   "rxcui": "161",   "info_type": "properties",   "data": {     "properties": {       "rxcui": "161",       "name": "ACETAMINOPHEN 325 MG TABLET",       "tty": "SCD",       "semanticType": "Clinical Drug"     }   } }</td></tr>
<tr><td style="word-wrap: break-word;">rxcui</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The single RxCUI string actually queried, extracted from the rxcuis list using the selected_rxcui index. This is convenient for downstream nodes or logic that need a scalar RxCUI rather than a list, and for logging or UI display of the active concept identifier.</td><td style="word-wrap: break-word;">"161"</td></tr>
<tr><td style="word-wrap: break-word;">status</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Human-readable message describing the outcome. On success it follows the pattern "Successfully retrieved <info_type> for RXCUI <rxcui>". On failure it reports issues such as index out of range, empty RxCUI, unknown info type, or upstream RxNorm API problems (prefixed with "API Error:" plus the underlying message).</td><td style="word-wrap: break-word;">"Successfully retrieved properties for RXCUI 161"</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Performance**: Each successful call performs exactly one external request to the RxNorm API for the selected RxCUI and info_type; if you iterate over many indices, consider batching or adding delays to avoid exceeding external service rate limits.
- **Limitations**: The rxcuis input must be valid JSON that decodes into a list; malformed JSON (e.g., single quotes, trailing commas) or non-list structures will lead to an error path where concept_info is {}, rxcui is empty, and status contains an error message.
- **Behavior**: If selected_rxcui is greater than or equal to the length of the decoded list, the node does not invoke the API and instead returns an empty payload and the status "Error: Selected RXCUI index out of range".
- **Behavior**: When the RxNorm service returns an error (for example due to an invalid RxCUI or network issue), that error is preserved inside data (under an error key) and the status is set to "API Error: <message>", allowing downstream logic to distinguish service failures from valid but sparse clinical data.

## Troubleshooting
- **Malformed rxcuis JSON**: If concept_info is {} and status mentions an error getting RxNorm concept info, check that rxcuis is valid JSON (use double quotes and square brackets, for example ["161","7052"]) and that it decodes to a list.
- **Index out of range**: If status is "Error: Selected RXCUI index out of range", the selected_rxcui value is not valid for the current list length. Inspect the decoded list length and adjust the index (e.g., use 0 when there is only one RxCUI).
- **Empty selected RxCUI**: If status is "Error: RXCUI cannot be empty", the chosen list element is an empty or whitespace-only string. Regenerate or clean the rxcuis list from upstream nodes to remove empty entries before calling this node.
- **Unexpected info type**: If status is "Error: Unknown info type: <value>", the info_type input is not one of name, ndcs, properties, or all_related. Reset info_type to a supported value to restore normal behavior.
