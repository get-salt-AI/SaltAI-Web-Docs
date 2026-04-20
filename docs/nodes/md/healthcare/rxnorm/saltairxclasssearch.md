# RxClass Search

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

This node queries the RxNorm RxClass service using a human-readable drug class name and returns the search results as a JSON string alongside a status message. It validates the input, invokes an internal RxNorm API helper, and wraps the service response in a consistent structure that includes the original search term. Errors are surfaced via both the JSON payload and the status output, allowing downstream logic to react appropriately.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/healthcare/rxnorm/saltairxclasssearch.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need to resolve a human-readable drug class name into standardized RxClass information, such as for clinical decision support, formulary analysis, or medication grouping workflows. It is typically placed after nodes that extract or normalize text inputs into candidate drug class names (for example, an LLM-based node that identifies "ACE Inhibitors" from a clinical note) and before nodes that require concrete class identifiers like `SaltAIRxClassInfo` or `SaltAIRxClassMembers`. A common pattern is: text or user input → normalization/extraction node → SaltAIRxClassSearch → parse JSON to obtain classId values → feed those IDs into downstream RxClass or RxNorm nodes for detailed metadata or drug lists. For best results, supply clear and standardized pharmacologic or therapeutic class names, and always inspect the status output and check the JSON for an error field before committing to any automated clinical or analytical decisions.

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
<tr><td style="word-wrap: break-word;">class_name</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The human-readable name of the drug class to search in RxClass. This must be a non-empty string; leading and trailing whitespace are ignored, but if the remaining content is empty, the node returns an error without calling the external service. It should correspond to recognized drug classes such as pharmacologic or therapeutic categories (e.g., "ACE Inhibitors", "Beta Blockers"), rather than arbitrary free-text or full clinical sentences.</td><td style="word-wrap: break-word;">ACE Inhibitors</td></tr>
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
<tr><td style="word-wrap: break-word;">class_search_results</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">A JSON-formatted string containing the RxClass search results. The structure wraps the underlying API response in an object of the form { "search_term": <input_class_name>, "results": <raw_results_from_RxNormAPI> }. The results field includes whatever data the RxNorm RxClass service returns for the query, typically containing one or more matching classes with identifiers and metadata. Downstream nodes should parse this string as JSON and extract items like classId, className, or classType for further operations or display.</td><td style="word-wrap: break-word;">{   "search_term": "ACE Inhibitors",   "results": {     "rxclassMinConceptList": {       "rxclassMinConcept": [         {           "classId": "123",           "className": "ACE Inhibitors",           "classType": "VA class"         },         {           "classId": "456",           "className": "ACE Inhibitors, plain",           "classType": "ETC"         }       ]     }   } }</td></tr>
<tr><td style="word-wrap: break-word;">status</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">A status message summarizing the outcome of the search. On success, it confirms that the RxClass search completed for the specified class name (for example, "Successfully searched RxClass for 'ACE Inhibitors'"). On validation failures, it reports the cause (such as "Error: Class name cannot be empty"). If the underlying RxNorm API helper returns an error, the status starts with "API Error:" followed by the error message. On unexpected internal exceptions, it begins with "Error during RxClass search:" and includes diagnostic details.</td><td style="word-wrap: break-word;">Successfully searched RxClass for 'ACE Inhibitors'</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Performance**: Each execution triggers a call to the RxNorm RxClass service via an internal API helper, so latency and throughput depend on external network and service availability. Avoid calling this node unnecessarily in high-frequency loops without caching or deduplication of identical queries.
- **Limitations**: Matching quality is constrained by the RxNorm RxClass dataset and API behavior. Non-standard terms, abbreviations, or misspellings (e.g., "ACEI" instead of "ACE Inhibitors") may yield no or incomplete results even when conceptually similar classes exist.
- **Behavior**: If class_name is empty or only whitespace after trimming, the node returns "{}" for class_search_results and a status of "Error: Class name cannot be empty", and no external API request is made.
- **Behavior**: When the API helper returns an error object containing an error field, the node serializes that object to JSON and sets the status to start with "API Error:" so that downstream components can reliably detect and handle remote failures.

## Troubleshooting
- **Empty or minimal results despite a plausible class name**: If the JSON output is structurally valid but contains no useful matches, the class name may not exactly match RxClass terminology. Try using a more canonical label, removing modifiers (e.g., "plain", dosage forms), or mapping abbreviations upstream before calling this node.
- **Status shows "Error: Class name cannot be empty"**: This indicates the class_name input is blank or resolves to only whitespace. Ensure that upstream nodes always supply a non-empty string and that conditional branches are configured so the node is not triggered on missing inputs.
- **Status starts with "API Error:"**: The RxNorm service or the RxNorm API helper encountered an issue (such as network failure or remote error). Inspect the JSON in class_search_results for the error field, implement retry logic where appropriate, and verify network connectivity and any necessary API configuration.
- **Status starts with "Error during RxClass search:"**: This reflects an unexpected internal exception, such as malformed or unexpected data from the API. Capture and review the full status message, and test with simpler or different class_name values to see if the issue is data-specific. If the problem is reproducible with a particular input, consider sanitizing or constraining that input upstream.
