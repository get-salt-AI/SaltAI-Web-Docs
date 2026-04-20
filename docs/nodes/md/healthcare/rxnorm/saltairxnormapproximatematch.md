# RxNorm Approximate Match

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

This node queries the RxNorm API for approximate matches to a user-supplied drug term, handling misspellings and close textual variants. It validates the input term, calls the RxNorm approximate match endpoint via the shared RxNormAPI helper, and wraps the response with the original search term for easier inspection. Results and errors are returned as formatted JSON strings along with a human-readable status message.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/healthcare/rxnorm/saltairxnormapproximatematch.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you have free-text medication names that may be misspelled, incomplete, or non-standard, and you need to map them to RxNorm concepts. It typically appears early in a workflow that normalizes medication text before more structured operations like concept lookup, interaction checking, or population analysis. A common pattern is to first send raw EHR text, pharmacy labels, or patient-entered medication names into RxNorm Approximate Match and then pass selected RxNorm concept identifiers into nodes like RxNorm Concept Info, RxNorm Related Concepts, or RxNorm Drug Interactions. Scenarios include cleaning up medication lists from clinical notes, reconciling patient self-reported drugs, or building a preprocessing step for downstream analytics. Integrations usually combine this node with RxNorm Drug Search when you want both exact/active-term matching and fuzzy matching, or with higher-level workflows like Population Medication Analysis after you have resolved messy inputs to canonical concepts. For best results, send reasonably specific drug terms (for example ingredient plus form or strength when available) and then review the returned JSON to choose the most appropriate RxCUI by score or rank before further processing.

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
<tr><td style="word-wrap: break-word;">term</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Free-text drug term to search for approximate RxNorm matches. The node trims whitespace and rejects empty strings. The term can include generic or brand names, and may contain typos or partial names; the underlying RxNorm approximate-match service will attempt fuzzy matching. Very short or ambiguous terms may return many or low-quality matches.</td><td style="word-wrap: break-word;">Tyleno 500mg tab</td></tr>
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
<tr><td style="word-wrap: break-word;">approximate_matches</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">A pretty-printed JSON string containing the original search term and the raw results from the RxNorm approximate match API. The structure is similar to: { "search_term": <string>, "results": <rxnorm-api-payload> }. The results payload typically includes candidate RxNorm CUIs and metadata such as match scores and names. Downstream nodes or custom logic can parse this JSON to select a specific RxCUI for further processing.</td><td style="word-wrap: break-word;">{   "search_term": "Tyleno 500mg tab",   "results": {     "approximateGroup": {       "inputTerm": "Tyleno 500mg tab",       "candidate": [         {           "rxcui": "161",           "score": "95",           "rank": "1",           "name": "acetaminophen 500 MG Oral Tablet"         },         {           "rxcui": "198440",           "score": "87",           "rank": "2",           "name": "TYLENOL 500 MG Oral Tablet"         }       ]     }   } }</td></tr>
<tr><td style="word-wrap: break-word;">status</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">A human-readable status message describing the outcome. On success it reports that approximate matches were retrieved for the given term; on validation or runtime failure it contains an error description (for example empty term or downstream API problems). This can be used for logging, user feedback, or branching logic in workflows.</td><td style="word-wrap: break-word;">Successfully retrieved approximate matches for 'Tyleno 500mg tab'</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Performance**: Each execution makes a live call to the RxNorm service via RxNormAPI, so latency depends on network and the external API; avoid unnecessarily tight loops over large term lists without batching or rate limiting.
- **Limitations**: If the term is empty or only whitespace, the node immediately returns "{}" for results with the status "Error: Term cannot be empty" and does not contact the external service.
- **Limitations**: The node returns the raw approximate-match payload without filtering or selecting a best candidate; you must parse the JSON and apply your own logic (for example choose highest score or specific concept type).
- **Behavior**: Any error reported by the underlying RxNorm API is surfaced inside the JSON as an error field and also mirrored in the status as API Error: <message>; unexpected exceptions are returned as a generic error status and an empty JSON object.

## Troubleshooting
- **Empty results JSON ({}) with status Error: Term cannot be empty**: The term input was blank or whitespace-only. Ensure you pass a non-empty string, for example by trimming and validating user input before connecting it to this node.
- **Status starts with API Error: and approximate_matches contains an error field**: The external RxNorm service rejected the request or is unavailable (network issues, rate limiting, or service outage). Retry later, check network connectivity from the environment where SaltAI is running, and consider adding retry or backoff logic in your workflow.
- **Unexpected error message in status like Error getting approximate matches: ...**: A runtime exception occurred (for example malformed response). Inspect any logs available in your SaltAI environment, verify that RxNormAPI configuration and endpoints are correct, and, if possible, capture and review the approximate_matches payload for clues.
- **Downstream node fails when parsing approximate_matches**: This output is a JSON string, not a structured object. Ensure the downstream consumer explicitly parses the string as JSON and handles cases where it may be "{}" or contain an error field instead of expected candidate lists.
