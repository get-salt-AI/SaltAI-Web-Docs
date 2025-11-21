# RxNorm Drug Search

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Searches the RxNorm database for drug concepts by name and returns a formatted JSON of the search results, the matched RxCUI list as JSON, and a status message. Supports optionally searching across all terms (not just active ones). Handles empty input and upstream API error responses gracefully.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/healthcare/rxnorm/saltairxnormsearch.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node to look up standardized RxNorm concept identifiers (RxCUIs) for a drug name before performing downstream operations such as concept info retrieval, related concepts exploration, or drug interaction checks. Typical workflow: provide a drug name, optionally enable broader term matching, then feed the returned RxCUI JSON into subsequent RxNorm nodes.

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
<tr><td style="word-wrap: break-word;">drug_name</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The drug name to search for in RxNorm.</td><td style="word-wrap: break-word;">acetaminophen</td></tr>
<tr><td style="word-wrap: break-word;">search_all_terms</td><td>True</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">If true, searches across all terms (not limited to active ones). If false, limits to active terms.</td><td style="word-wrap: break-word;">false</td></tr>
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
<tr><td style="word-wrap: break-word;">search_results</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">A pretty-printed JSON string containing the original search term, the search_all_terms flag, and the raw API results.</td><td style="word-wrap: break-word;">{"search_term": "acetaminophen", "search_all_terms": false, "results": {"idGroup": {"name": "acetaminophen", "rxnormId": ["12345", "67890"]}}}</td></tr>
<tr><td style="word-wrap: break-word;">rxcui</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">JSON stringified array of matched RxCUI identifiers extracted from the results.</td><td style="word-wrap: break-word;">["12345", "67890"]</td></tr>
<tr><td style="word-wrap: break-word;">status</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Human-readable message indicating success or the type of error encountered.</td><td style="word-wrap: break-word;">Successfully searched for 'acetaminophen' in RxNorm</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Outputs are JSON strings**: Both search_results and rxcui are JSON-encoded strings. Parse them if you need structured data downstream.
- **Empty input check**: If drug_name is empty or whitespace, the node returns search_results="{}", rxcui="[]", and an error status.
- **API error handling**: If the upstream service returns an error, the node returns the error payload in search_results, rxcui="[]", and a status starting with "API Error:"
- **search_all_terms broadens matching**: Turn this on to include non-active or additional term variants, which may increase the number of RxCUIs returned.
- **Network dependency**: This node relies on an external RxNorm API; connectivity or service interruptions can affect results and timing.

## Troubleshooting
- **Received 'Error: Drug name cannot be empty'**: Provide a non-empty drug_name value.
- **Status shows 'API Error:'**: Inspect search_results for the error details. Retry later, check network connectivity, or adjust the input term.
- **rxcui is an empty array**: Either no matches were found or the request errored. Try enabling search_all_terms, use a more general name, or correct spelling.
- **Downstream nodes fail to read outputs**: Ensure you parse the JSON strings from search_results and rxcui before accessing fields or iterating.
