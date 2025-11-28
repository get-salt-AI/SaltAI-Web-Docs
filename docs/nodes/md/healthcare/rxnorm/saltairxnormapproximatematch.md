# RxNorm Approximate Match

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Finds approximate RxNorm matches for a free-text term (e.g., misspelled or partial drug names). It returns a JSON string containing the search term and the API's match results, along with a status message indicating success or error.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/healthcare/rxnorm/saltairxnormapproximatematch.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you have a non-standard or potentially misspelled drug term and need to identify likely RxNorm concepts. Typical workflow: provide a user-entered term, get approximate matches, then pass the resulting JSON to downstream nodes or logic to select a specific RxCUI for further lookups.

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
<tr><td style="word-wrap: break-word;">term</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The free-text drug term to match (supports partial or misspelled names).</td><td style="word-wrap: break-word;">acetominophen</td></tr>
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
<tr><td style="word-wrap: break-word;">approximate_matches</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">A JSON string with the search term and the approximate match results from RxNorm.</td><td style="word-wrap: break-word;">{   "search_term": "acetominophen",   "results": {     "approximateGroup": {       "candidate": [         { "rxcui": "161", "score": "100", "rxnormId": "161", "name": "Acetaminophen" }       ]     }   } }</td></tr>
<tr><td style="word-wrap: break-word;">status</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Status message indicating success or describing the error encountered.</td><td style="word-wrap: break-word;">Successfully retrieved approximate matches for 'acetominophen'</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Input validation**: An empty term returns "{}" for approximate_matches and "Error: Term cannot be empty" as status.
- **Output format**: approximate_matches is a JSON string. Parse it downstream to access fields like candidates, names, or RxCUIs.
- **API errors**: If the external service returns an error, the node returns the error JSON and a status starting with "API Error:".
- **Default behavior**: If no input is provided, the default term is "acetaminophen".
- **Variability**: The structure inside results may vary based on the external API; always check keys like approximateGroup/candidate before accessing.

## Troubleshooting
- **Empty input returns an error**: Provide a non-empty term. The node will not proceed with blank input.
- **No matches found**: The results array may be empty. Try a more general term or a correct spelling.
- **Parsing issues downstream**: Ensure you parse the approximate_matches string as JSON before accessing fields.
- **API Error status**: Network or service issues will produce a status like "API Error: ...". Retry later or verify connectivity.
- **Unexpected results structure**: The external API may change response shapes. Add defensive checks when extracting candidates and IDs.
