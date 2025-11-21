# RxNorm Approximate Match

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Finds approximate RxNorm matches for a free-text term (e.g., a common drug name or misspelling). It queries the RxNorm service and returns the raw results as a JSON string along with a status message. Useful for fuzzy matching when exact concept names or identifiers are unknown.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/healthcare/rxnorm/saltairxnormapproximatematch.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you have a user-entered or noisy medication string and need to discover likely RxNorm concepts. Provide a single term (like 'acetamenophen' or a brand name). The node returns a JSON string containing the search term and the RxNorm API results. Downstream nodes can parse this JSON to extract candidate RxCUIs or display suggestions.

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
<tr><td style="word-wrap: break-word;">term</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The free-text term to approximate-match against RxNorm (drug name, brand, or potentially misspelled term).</td><td style="word-wrap: break-word;">acetaminophen</td></tr>
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
<tr><td style="word-wrap: break-word;">approximate_matches</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">A JSON string containing the search term and the RxNorm approximate-match results. Structure: { "search_term": <term>, "results": <RxNorm API response> }.</td><td style="word-wrap: break-word;">{   "search_term": "acetaminophen",   "results": { "approximateGroup": { "candidate": [ { "rxcui": "161", "rxaui": "...", "score": "100", "name": "Acetaminophen" } ] } } }</td></tr>
<tr><td style="word-wrap: break-word;">status</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Human-readable status message indicating success or the error encountered.</td><td style="word-wrap: break-word;">Successfully retrieved approximate matches for 'acetaminophen'</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Input required**: The term must be a non-empty string; an empty or whitespace-only term returns "{}" and an error status.
- **Raw API structure**: The node returns the RxNorm approximate-match results without normalization; downstream logic should parse the JSON and handle fields like scores, candidate lists, and RxCUIs.
- **Error handling**: If the RxNorm service responds with an error (results contain an "error" key), the node returns that error payload in the JSON and a corresponding API error status.
- **Output types**: Both outputs are strings; parse the first output as JSON before extracting values.
- **Service dependency**: Results depend on the availability and behavior of the RxNorm API; network or service issues can impact responses.

## Troubleshooting
- **Empty results or '{}' output**: Ensure the 'term' input is not empty and does not contain only whitespace.
- **API Error in status**: The returned JSON likely includes an "error" key from RxNorm; inspect the 'approximate_matches' JSON for details and retry later or adjust the term.
- **Unexpected JSON structure**: RxNorm responses can vary; log or print the JSON to confirm keys like 'approximateGroup' and 'candidate' are present before indexing.
- **Unicode or special characters**: If the term includes special characters, try a simplified ASCII version or a common synonym.
- **Downstream parsing errors**: Remember the output is a string; parse it as JSON in subsequent steps before accessing fields like 'candidate' or 'rxcui'.
