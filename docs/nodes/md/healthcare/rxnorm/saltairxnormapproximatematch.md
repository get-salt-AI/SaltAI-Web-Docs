# RxNorm Approximate Match

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Finds approximate RxNorm matches for a free-text drug term, useful for handling misspellings or variant drug names. Returns a JSON string of results and a status message. Designed to quickly map user-entered medication terms to likely RxNorm concepts.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/healthcare/rxnorm/saltairxnormapproximatematch.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you have a free-text drug name and need likely RxNorm matches (e.g., from user input or messy datasets). Typical workflow: run Approximate Match on a term, inspect returned candidate RxCUIs, then feed selected RxCUI(s) into nodes like RxNorm Concept Info, Related Concepts, or Drug Interactions.

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
<tr><td style="word-wrap: break-word;">term</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The free-text drug term to search for approximate matches. Supports common drug names and misspellings.</td><td style="word-wrap: break-word;">acetaminophen</td></tr>
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
<tr><td style="word-wrap: break-word;">approximate_matches</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">A JSON string containing the search term and the approximate match results returned by RxNorm.</td><td style="word-wrap: break-word;">{ "search_term": "acetaminophen", "results": { ... } }</td></tr>
<tr><td style="word-wrap: break-word;">status</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">A human-readable message indicating success or describing an error.</td><td style="word-wrap: break-word;">Successfully retrieved approximate matches for 'acetaminophen'</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Empty term handling**: If the input term is empty or whitespace-only, the node returns an empty JSON object and an error status.
- **API errors**: If the underlying service returns an error, it is surfaced in the status and the results JSON will contain the error information.
- **Output format**: The first output is a pretty-printed JSON string with keys 'search_term' and 'results'.
- **Result selection**: This node does not choose a single best match; review results and select appropriate RxCUI(s) for downstream steps.

## Troubleshooting
- **Empty or missing term**: Ensure 'term' is a non-empty string. The node will error on blank input.
- **No matches found**: Try a simpler term, remove dosage/strength, or correct spelling.
- **API/network issues**: On timeouts or API rate limits, retry later or check connectivity. The status output will include any API error messages.
- **Unexpected JSON structure**: The results reflect the external service response; validate the JSON before parsing and handle missing fields defensively.
