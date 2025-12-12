# RxNorm Approximate Match

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Finds approximate RxNorm matches for a free-text term. Useful for fuzzy matching when the input may include misspellings, brand/generic variations, or partial names. Returns a JSON string of results and a status message.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/healthcare/rxnorm/saltairxnormapproximatematch.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need to map user-entered or noisy medication terms to likely RxNorm concepts. Typical workflow: pass a user-entered drug name into this node to retrieve candidate matches, optionally feed resulting identifiers to other RxNorm nodes for detailed information.

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
<tr><td style="word-wrap: break-word;">term</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The drug term to search for with approximate matching. Accepts a single free-text string.</td><td style="word-wrap: break-word;">acetominophen</td></tr>
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
<tr><td style="word-wrap: break-word;">approximate_matches</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">JSON string containing the search term and the approximate match results returned by the underlying RxNorm service.</td><td style="word-wrap: break-word;">{"search_term": "acetominophen", "results": {"candidate": [{"rxcui": "161", "score": "~", "name": "Acetaminophen"}]}}</td></tr>
<tr><td style="word-wrap: break-word;">status</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Human-readable status message indicating success or describing any error.</td><td style="word-wrap: break-word;">Successfully retrieved approximate matches for 'acetominophen'</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Input required**: The term cannot be empty; an empty term returns an error status.
- **Output format**: The first output is a JSON-formatted string. Parse it before downstream processing that expects structured data.
- **Result variability**: The structure of "results" mirrors the upstream service and may include candidates, scores, and identifiers; exact fields can vary.
- **Not a direct resolver**: This node surfaces candidates but does not guarantee an exact concept. Use a subsequent node to fetch details for a selected RxCUI.
- **Error handling**: If the upstream service reports an error, the first output contains the error payload as JSON and the status starts with "API Error:".

## Troubleshooting
- **Empty or blank term**: Returns "{}" and "Error: Term cannot be empty". Provide a non-empty string.
- **API Error in status**: The status will read "API Error: ..." and the first output will include error details. Retry later or validate the term.
- **Unexpected JSON shape**: The "results" structure may change. Inspect and adapt downstream parsing logic accordingly.
- **No matches found**: You may get an empty candidate list. Try alternative spellings, include more context, or use the RxNorm Search node for exact matches.
- **Encoding or special characters**: If the term includes special characters and yields errors, simplify the input (e.g., remove trademarks or symbols) and retry.
