# RxNorm Drug Search

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Searches the RxNorm database for drug concepts by name. You can choose to search across all source terms or only active terms. Returns the full API response as JSON, a JSON array of matching RxCUIs, and a status message.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/healthcare/rxnorm/saltairxnormsearch.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need to resolve a free-text drug name into RxNorm concepts. Typically, you start with this node to obtain one or more RxCUIs and then feed those identifiers into downstream nodes to fetch properties, RxTerms info, related concepts, or other analyses.

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
<tr><td style="word-wrap: break-word;">drug_name</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The drug name to search for in RxNorm. Free text is accepted.</td><td style="word-wrap: break-word;">acetaminophen</td></tr>
<tr><td style="word-wrap: break-word;">search_all_terms</td><td>True</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">If true, searches all source terms; if false, searches only active terms.</td><td style="word-wrap: break-word;">false</td></tr>
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
<tr><td style="word-wrap: break-word;">search_results</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">A pretty-printed JSON string containing the search parameters and the raw RxNorm API response.</td><td style="word-wrap: break-word;">{   "search_term": "acetaminophen",   "search_all_terms": false,   "results": { "idGroup": { "rxnormId": ["161"] } } }</td></tr>
<tr><td style="word-wrap: break-word;">rxcui</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">A JSON array string of matched RxCUI identifiers extracted from the search results.</td><td style="word-wrap: break-word;">["161", "798928"]</td></tr>
<tr><td style="word-wrap: break-word;">status</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">A human-readable status summarizing success or any encountered error.</td><td style="word-wrap: break-word;">Successfully searched for 'acetaminophen' in RxNorm</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Empty input handling**: If the drug_name is blank or whitespace, the node returns "{}", "[]", and an error status without calling the API.
- **External API dependency**: This node calls the RxNav REST API; network issues or service errors will be reflected as an API error message in the status and search_results output.
- **RXCUI output format**: The rxcui output is a JSON array serialized as a string. Downstream consumers may need to parse it back into an array.
- **Result structure**: The node expects RxNorm's find endpoint to return idGroup.rxnormId. If that structure is missing due to no matches or API changes, the node returns an error payload and empty rxcui.
- **Search scope**: 'search_all_terms' toggles whether to include all source terms vs. only active terms, which can significantly affect the number and type of matches.

## Troubleshooting
- **No results returned**: Ensure the drug_name is spelled correctly or enable search_all_terms to broaden the query.
- **Empty RXCUI list**: This can occur if there are no matches or the API response lacks idGroup.rxnormId. Try a broader name or verify the API is reachable.
- **API/network errors**: Check network connectivity and retry. The status will include a message like "API Error: <message>" when RxNav is unavailable or returns an error.
- **Unexpected output format**: The rxcui field is a JSON array as a string. If a downstream step expects a list, parse it first.
- **Input validation error**: If you see "Error: Drug name cannot be empty", provide a non-empty string for drug_name.
