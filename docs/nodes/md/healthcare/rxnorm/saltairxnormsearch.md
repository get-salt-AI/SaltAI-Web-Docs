# RxNorm Drug Search

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

This node queries the RxNorm service for drug concepts that match a given drug name. It returns a formatted JSON of the full search response, a JSON array of RxCUI identifiers, and a human-readable status string. It serves as the primary entry point for turning free-text medication names into standardized RxNorm concepts for downstream clinical or analytics workflows.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/healthcare/rxnorm/saltairxnormsearch.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need to normalize a free-text medication name into standardized RxNorm concepts. Common scenarios include cleaning EHR medication lists, mapping patient-entered drug names to clinical vocabularies, or preparing medication data for decision support or interaction checking. In a typical workflow, upstream nodes collect or generate a drug name string from user input, file ingestion, or NLP over clinical notes; that string is then provided to `SaltAIRxNormSearch`.

Downstream, the `rxcui` output is usually consumed by `SaltAIRxNormConceptInfo` (to retrieve detailed concept attributes such as ingredient, brand, and form), `SaltAIRxNormRelatedConcepts` (to explore generic/brand/ingredient relationships), or `SaltAIRxNormDrugInteractions` (to assess interaction risks between medications). The full `search_results` JSON is helpful for debugging, audit logging, or building UIs that display all matching RxNorm concepts for user selection.

Best practices: provide reasonably specific drug names (e.g., “metformin 500 mg tablet” instead of just “metformin”) to get more precise matches; use `search_all_terms = false` in production pipelines where you want current, standard concepts, and set it to true when you are doing retrospective analyses or need maximum coverage, even if that introduces more ambiguous or historical terms.

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
<tr><td style="word-wrap: break-word;">drug_name</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The free-text name of the drug to search in RxNorm. It may include brand or generic names and can optionally contain strength or dosage form text (for example, “500 mg tablet”). It must not be empty or only whitespace; overly vague strings may return no matches or a large set of ambiguous matches.</td><td style="word-wrap: break-word;">metformin 500 mg tablet</td></tr>
<tr><td style="word-wrap: break-word;">search_all_terms</td><td>True</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">Whether to search across all RxNorm terms or focus on active concepts only. When false, the search is typically limited to currently active, standard concepts. When true, the search may include historical or less commonly used terms, which can improve match coverage but may also increase ambiguity in results.</td><td style="word-wrap: break-word;">false</td></tr>
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
<tr><td style="word-wrap: break-word;">search_results</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">A pretty-printed JSON string containing the full RxNorm search response plus metadata. It includes the original `search_term`, the `search_all_terms` flag, and a `results` object mirroring the response from the RxNorm API. Use this for inspection, logging, or presenting all match candidates in a UI.</td><td style="word-wrap: break-word;">{   "search_term": "metformin 500 mg tablet",   "search_all_terms": false,   "results": {     "idGroup": {       "name": "metformin 500 mg tablet",       "rxnormId": ["860975", "860976"]     }   } }</td></tr>
<tr><td style="word-wrap: break-word;">rxcui</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">A JSON-encoded array of RxCUI identifiers extracted from the `results.idGroup.rxnormId` field in the RxNorm response. This is the primary normalized identifier list to feed into downstream RxNorm nodes. If no identifiers are found or an error occurs, this value is an empty JSON array string ("[]").</td><td style="word-wrap: break-word;">["860975", "860976"]</td></tr>
<tr><td style="word-wrap: break-word;">status</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">A human-readable status message indicating the outcome of the search. On success, it confirms the term that was searched; on error, it describes the problem, such as invalid input or an API error. This output is useful for logs, monitoring, or displaying feedback in user interfaces.</td><td style="word-wrap: break-word;">Successfully searched for 'metformin 500 mg tablet' in RxNorm</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Performance**: Each run performs an external request through the RxNorm API wrapper, so latency depends on network conditions and remote service responsiveness; avoid calling this node in very tight loops without batching or throttling.
- **Limitations**: If `drug_name` is empty or only whitespace, the node does not contact RxNorm and instead returns an empty JSON object for `search_results`, an empty array string for `rxcui`, and a status message explaining that the drug name cannot be empty.
- **Behavior**: When the RxNorm API indicates an error, the node wraps the error information in the `search_results` JSON, returns `"[]"` for `rxcui`, and sets the status to a string beginning with `API Error:` so that automated monitoring or conditional logic can detect failures reliably.
- **Behavior**: The `rxcui` output is always a string containing JSON (typically a list of IDs), not a native array type. Downstream nodes in the RxNorm suite understand this format; if you process it manually, remember to JSON-decode it before iterating over IDs.

## Troubleshooting
- **Common Error 1**: Status is `Error: Drug name cannot be empty`, with `search_results` equal to `{}` and `rxcui` equal to `"[]"`. Cause: the `drug_name` input was blank or whitespace-only. Resolution: ensure upstream validation enforces a non-empty medication name and trims input before invoking this node.
- **Common Error 2**: Status begins with `API Error:` and `rxcui` is `"[]"`. Cause: the RxNorm service call failed or returned an error (network outage, rate limiting, or malformed response). Resolution: verify network connectivity and the configuration of the RxNorm integration; implement retry or fallback behavior in your workflow as appropriate.
- **Common Error 3**: `search_results` is populated but `rxcui` is `"[]"` or missing expected IDs. Cause: RxNorm did not return an `idGroup.rxnormId` field for the given string, often because the text was too vague, misspelled, or overly detailed. Resolution: simplify or correct the `drug_name` (for example, remove nonstandard abbreviations), and consider setting `search_all_terms` to true to broaden the search space.
