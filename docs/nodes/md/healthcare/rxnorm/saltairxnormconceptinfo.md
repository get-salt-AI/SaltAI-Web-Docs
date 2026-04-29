# RxNorm Concept Info

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

This node retrieves detailed information about a specific RxNorm concept (RxCUI) chosen from a list, using the RxNorm API. It can return the concept's name, NDCs, core properties, or comprehensive related information, formatted as readable JSON. It is designed to be used after a search or list-generation step where multiple RxCUIs are available and one needs deeper inspection.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/healthcare/rxnorm/saltairxnormconceptinfo.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you already have one or more RxCUIs (for example from a prior drug search or NDC lookup) and need detailed information about a particular concept. A typical workflow is: first call `SaltAIRxNormSearch` to search for a drug name and get back a list of candidate RxCUIs, then pass that list into `SaltAIRxNormConceptInfo` and use `selected_rxcui` to choose which candidate to inspect, based on index position. Depending on `info_type`, you can retrieve the canonical drug name (`name`), all associated NDCs (`ndcs`), concept metadata such as term type, status, and ingredient relationships (`properties`), or an aggregated view of all related information (`all_related`). Upstream nodes typically include `SaltAIRxNormSearch` (for name-based search) or `SaltAIRxCUIByNDC` (for NDC-based lookup), which produce RxCUIs that you serialize as a JSON list string for this node's `rxcuis` input. Downstream, the `concept_info` JSON string can be consumed by generic JSON parsing/analysis nodes (for example, for filtering, mapping to EHR fields, or feeding into clinical decision support logic) or additional RxNorm nodes like `SaltAIRxNormRelatedConcepts` and `SaltAIRxNormDrugInteractions`. For best results, ensure `rxcuis` is always valid JSON (a list of strings) and that `selected_rxcui` points to a valid index in that list. Use `properties` for lean metadata retrieval, and `all_related` only when you truly need a rich, potentially large data structure.

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
<tr><td style="word-wrap: break-word;">rxcuis</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">JSON-encoded list of RxNorm Concept Unique Identifiers (RxCUIs) as strings. The node will pick exactly one RxCUI from this list based on `selected_rxcui`. It must be valid JSON representing an array; each entry should be a non-empty RxCUI, typically a numeric string such as "161". If the value is not parseable JSON or is not a list, the node will fail and return an error status.</td><td style="word-wrap: break-word;">["161", "313", "617314"]</td></tr>
<tr><td style="word-wrap: break-word;">selected_rxcui</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Zero-based index into the `rxcuis` list that determines which RxCUI will be queried. Must be an integer between 0 and len(rxcuis)-1. If this index is greater than or equal to the number of items in the list, the node returns an error status indicating the selected RXCUI index is out of range.</td><td style="word-wrap: break-word;">1</td></tr>
<tr><td style="word-wrap: break-word;">info_type</td><td>True</td><td style="word-wrap: break-word;">STRING (enum: name \| ndcs \| properties \| all_related)</td><td style="word-wrap: break-word;">Specifies what type of information to retrieve for the selected RxCUI. `name` returns the canonical RxNorm name; `ndcs` returns NDCs associated with this concept; `properties` returns a focused set of concept properties; `all_related` returns a comprehensive set of related information. Any other value is treated as invalid and will produce an error status.</td><td style="word-wrap: break-word;">properties</td></tr>
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
<tr><td style="word-wrap: break-word;">concept_info</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">A pretty-printed JSON string containing the requested information for the chosen RxCUI. The structure always wraps the returned data in an object with `rxcui`, `info_type`, and `data` fields, where `data` holds the raw RxNorm API response for the requested info type.</td><td style="word-wrap: break-word;">{   "rxcui": "313",   "info_type": "properties",   "data": {     "idGroup": {       "rxnormId": ["313"]     },     "propConceptGroup": {       "propConcept": [         {           "propName": "NAME",           "propValue": "warfarin 5 MG Oral Tablet"         },         {           "propName": "TTY",           "propValue": "SCD"         }       ]     }   } }</td></tr>
<tr><td style="word-wrap: break-word;">rxcui</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The specific RxCUI string that was actually queried, extracted from the `rxcuis` list using `selected_rxcui`. This is empty when there is an error before a valid RxCUI is determined (for example, index out of range or empty RxCUI).</td><td style="word-wrap: break-word;">313</td></tr>
<tr><td style="word-wrap: break-word;">status</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Human-readable status message describing the outcome of the call. On success, it reports which info type was retrieved for which RxCUI. On failure, it contains an error description, such as index range issues, missing or empty RxCUI, invalid info type, or an API error message.</td><td style="word-wrap: break-word;">Successfully retrieved properties for RXCUI 313</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Performance**: The `all_related` info_type can result in larger, more complex responses and may be slower than `name` or `properties`, especially for widely used medications.
- **Limitations**: The node depends on the external RxNorm API via an internal `RxNormAPI` client; network issues or RxNorm service downtime will cause API error responses surfaced through the `status` output.
- **Behavior**: The `rxcuis` input must be valid JSON representing a list; malformed JSON or a wrong shape (for example, an object instead of an array) will lead to an exception and a generic error message being returned.
- **Behavior**: If `selected_rxcui` points outside the bounds of the parsed list, the node short-circuits and returns an explicit "Error: Selected RXCUI index out of range" status without calling the API.
- **Behavior**: If the RxNorm API returns an error payload (containing an `error` field), the node wraps that payload in JSON, leaves the `rxcui` output empty, and prefixes the `status` with `API Error:`.

## Troubleshooting
- **Error: Selected RXCUI index out of range**: This occurs when `selected_rxcui` is greater than or equal to the length of the `rxcuis` list. Verify that `rxcuis` contains the expected number of entries and that you are using zero-based indexing (first item is index 0).
- **Error: RXCUI cannot be empty**: One or more entries in the `rxcuis` list are empty strings or whitespace. Ensure that upstream nodes or manual inputs provide real RxCUIs and that you do not include empty items in the JSON list.
- **API Error: <message>**: The RxNorm service returned an error (for example, invalid RxCUI, service unavailable). Check that the chosen RxCUI is valid, retry later if it might be a transient service issue, and consider fallbacks such as trying another candidate RxCUI from the list.
- **Error getting RxNorm concept info: <details>**: A generic error indicating parsing or runtime issues (often malformed JSON in `rxcuis`). Confirm that `rxcuis` is a well-formed JSON array of strings and that no extra characters like trailing commas or comments are present.
