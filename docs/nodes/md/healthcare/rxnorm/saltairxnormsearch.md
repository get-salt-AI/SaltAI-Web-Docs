# RxNorm Drug Search

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Searches the RxNorm database for drug concepts by name and returns the full search payload, the list of RxCUI identifiers, and a status message. Supports toggling whether to consider all term types versus only active terms.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/healthcare/rxnorm/saltairxnormsearch.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need to resolve a drug name into its RxNorm identifiers (RxCUIs) and inspect the raw search results. It typically serves as the first step in RxNorm-driven workflows, where the returned RxCUIs are fed into downstream nodes for concept details, related concepts, or interaction checks.

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
<tr><td style="word-wrap: break-word;">drug_name</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The drug name to look up in RxNorm. Leading and trailing whitespace is ignored; must not be empty.</td><td style="word-wrap: break-word;">acetaminophen</td></tr>
<tr><td style="word-wrap: break-word;">search_all_terms</td><td>True</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">If true, searches all available term types in RxNorm; if false, restricts to active or preferred terms.</td><td style="word-wrap: break-word;">false</td></tr>
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
<tr><td style="word-wrap: break-word;">search_results</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">A JSON-formatted string containing the search summary and the raw RxNorm response. Includes the original search term, the flag used, and the results object.</td><td style="word-wrap: break-word;">{   "search_term": "acetaminophen",   "search_all_terms": false,   "results": { "idGroup": { "name": "acetaminophen", "rxnormId": ["161"] } } }</td></tr>
<tr><td style="word-wrap: break-word;">rxcui</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">A JSON-formatted string representing the list of RxCUI identifiers extracted from the search results.</td><td style="word-wrap: break-word;">["161"]</td></tr>
<tr><td style="word-wrap: break-word;">status</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Human-readable status message indicating success or error details.</td><td style="word-wrap: break-word;">Successfully searched for 'acetaminophen' in RxNorm</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Input validation**: The drug_name must not be empty. If empty, the node returns '{}' for search_results, '[]' for rxcui, and an error status.
- **API error handling**: If the underlying RxNorm request returns an error, the node outputs the error payload in search_results, an empty list for rxcui, and a status prefixed with 'API Error:'.
- **Output format**: All outputs are strings. search_results and rxcui are JSON-encoded strings; parse them if you need structured data downstream.
- **Search scope**: The search_all_terms flag changes the scope of what RxNorm considers (e.g., broader term sets when true). Results and returned RxCUIs may differ based on this setting.
- **Result shape dependency**: The node expects standard RxNorm response structure with idGroup.rxnormId present. If no identifiers are found, rxcui may be an empty list.

## Troubleshooting
- **Empty RxCUI list**: If rxcui is '[]', verify the spelling of drug_name and consider setting search_all_terms to true to broaden the search.
- **'Error: Drug name cannot be empty'**: Provide a non-empty drug_name value.
- **'API Error:' status**: Inspect search_results for the error details (e.g., rate limiting or connectivity). Retry later or check network configuration.
- **JSON parsing issues downstream**: Remember that search_results and rxcui are JSON strings. Parse them before accessing fields.
