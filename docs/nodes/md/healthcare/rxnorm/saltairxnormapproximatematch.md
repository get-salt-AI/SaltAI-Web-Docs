# RxNorm Approximate Match

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

This node queries the RxNorm API for approximate matches to a user-provided term such as a drug name or misspelled medication. It wraps the RxNorm approximate match endpoint and returns a JSON-formatted response along with a human-readable status message. It is designed for normalizing noisy or inconsistent medication strings into structured RxNorm concepts.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/healthcare/rxnorm/saltairxnormapproximatematch.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you have free-text medication terms that may contain spelling errors, abbreviations, or partial names and you want to map them to likely RxNorm concepts. It typically appears early in a medication normalization or clinical data cleaning workflow, after ingesting patient-entered medications, clinical notes, or external drug lists. Upstream, it usually receives strings from data ingestion, OCR, or NLP extraction nodes. Downstream, the JSON output is commonly parsed to extract candidate RxCUIs and scores, which can then be passed into nodes like "SaltAIRxNormConceptInfo" for detailed metadata, or into custom selection/filtering logic to pick the best match. A practical pattern is: (1) call this node for each raw term, (2) parse the `approximate_matches` JSON for candidates and their scores, (3) apply minimum score thresholds or business rules, and (4) only send accepted RxCUIs forward for further analysis or storage.

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
<tr><td style="word-wrap: break-word;">term</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Free-text medication term to resolve into RxNorm concepts. Typically a generic or brand drug name, possibly misspelled or with dosage/form descriptors. Must be a non-empty string after trimming; otherwise the node returns an error without calling RxNorm. There is no explicit length check in the node, but excessively long strings may cause RxNorm API failures or poor matches.</td><td style="word-wrap: break-word;">tylenol extra strengh 500mg tablet</td></tr>
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
<tr><td style="word-wrap: break-word;">approximate_matches</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">A JSON-formatted string containing the approximate match results from RxNorm. The node wraps the RxNorm response in an object with `search_term` (the original input) and `results` (the raw API payload). Within `results`, you typically see an `approximateGroup` with one or more candidate entries, each providing an RxCUI, a score, and a name. Downstream nodes should parse this string as JSON before using it for decision-making or further lookups.</td><td style="word-wrap: break-word;">{"search_term":"tylenol extra strengh 500mg tablet","results":{"approximateGroup":{"inputTerm":"tylenol extra strengh 500mg tablet","candidate":[{"rxcui":"198440","score":"98","rank":"1","name":"Tylenol Extra Strength 500 MG Oral Tablet"},{"rxcui":"161","score":"85","rank":"2","name":"Acetaminophen 500 MG Oral Tablet"}]}}}</td></tr>
<tr><td style="word-wrap: break-word;">status</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">A human-readable status message describing the outcome of the operation. On success, it confirms that approximate matches were retrieved for the given term. On validation failure (for example, empty term) or RxNorm API failure, it contains an error message that you can use for logging, user feedback, or conditional routing in a workflow.</td><td style="word-wrap: break-word;">Successfully retrieved approximate matches for 'tylenol extra strengh 500mg tablet'</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Performance**: Each run performs a live call to the RxNorm API via the RxNormAPI client; total latency depends on network and external service responsiveness. Avoid high-volume parallel calls without considering rate limits or batching.
- **Limitations**: The node does not implement retries or advanced error handling. If the RxNorm service is unavailable or returns an error payload, that error is simply wrapped into the JSON and status message for you to handle upstream.
- **Behavior**: If the `term` input is empty or only whitespace, the node skips the API call and returns "{}" for `approximate_matches` and a status string of "Error: Term cannot be empty".
- **Behavior**: The `approximate_matches` output is always a plain string that contains JSON; it is not a structured object. You must explicitly parse it in downstream logic to access fields like candidate RxCUIs, scores, or names.

## Troubleshooting
- **Empty output with error status**: If `approximate_matches` is "{}" and the status is "Error: Term cannot be empty", check that the upstream node passes a non-empty `term` value and that leading/trailing whitespace is handled correctly.
- **API Error in status**: If the status starts with "API Error:" and the JSON in `approximate_matches` includes an `error` field, the RxNorm API rejected the request or was unavailable. Verify network connectivity, ensure that request volume is within acceptable limits, and consider adding retry or backoff logic around this node.
- **Parsing failures downstream**: If downstream nodes or scripts fail when parsing `approximate_matches`, log or inspect the raw string to confirm it is valid JSON. Adjust your parser to handle cases where there are no candidates (e.g., missing `candidate` array) or where the overall `results` structure differs based on the RxNorm response.
