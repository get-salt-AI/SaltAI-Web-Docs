# RxNorm Approximate Match

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Finds approximate (fuzzy) RxNorm matches for a free-text term. It queries the RxNorm service and returns a JSON string containing the search term and the service’s approximate match results, along with a status message.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/healthcare/rxnorm/saltairxnormapproximatematch.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you have an imprecise or free-text medication term (e.g., misspellings or partial names) and you need likely RxNorm concept matches. Typical workflow: provide a drug term, run the node to get approximate matches as JSON, then downstream nodes or scripts can parse that JSON to select specific RxCUIs or concepts.

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
<tr><td style="word-wrap: break-word;">term</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The free-text drug term to search for approximate RxNorm matches.</td><td style="word-wrap: break-word;">acetaminophen</td></tr>
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
<tr><td style="word-wrap: break-word;">approximate_matches</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">A pretty-printed JSON string with the search term and the RxNorm approximate match results.</td><td style="word-wrap: break-word;">{"search_term": "acetaminophen", "results": {"approximateGroup": {"candidate": [{"rxcui": "161", "score": "100"}]}}}</td></tr>
<tr><td style="word-wrap: break-word;">status</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Human-readable status message indicating success or describing any error.</td><td style="word-wrap: break-word;">Successfully retrieved approximate matches for 'acetaminophen'</td></tr>
</tbody>
</table>
</div>

## Important Notes
- The input term must be non-empty; an empty term returns "{}" and an error status.
- The 'approximate_matches' output is a JSON string; parse it before using individual fields (e.g., candidates, RxCUIs).
- If the underlying RxNorm service returns an error, the node passes it through in the JSON and status as an API error.
- Results can include multiple candidates with scores; the node does not filter or select a single best match.
- This node depends on external RxNorm API availability and may be impacted by network issues or service rate limits.
- Category: SALT/RxNorm/Utilities

## Troubleshooting
- Input is empty: Ensure 'term' contains a non-blank string.
- API Error in status: Retry later and verify network access; if persistent, validate the term or check RxNorm service availability.
- Output not parseable: Confirm you are parsing the 'approximate_matches' field as JSON text.
- No matches returned: Try alternative spellings or broader terms to improve approximate matching.
