# RxClass Information

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

This node queries the RxNorm RxClass service to get detailed information about a drug class identified by its class ID. It validates the provided class ID, calls the RxNorm API helper, and wraps the response into a JSON structure containing the input class ID and the returned data. Both the JSON string and a human-readable status message are produced, with API errors surfaced in a structured way.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/healthcare/rxnorm/saltairxclassinfo.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you already know the RxClass identifier for a drug class and need detailed metadata about that class for clinical, research, or analytics workflows. A common workflow is to first discover classes by name using `SaltAIRxClassSearch`, then supply a selected `class_id` to `SaltAIRxClassInfo` for detailed information. Place it mid-pipeline: upstream nodes or configuration provide the `class_id`, and downstream nodes parse and consume the JSON for reporting, decision support, or visualization. It pairs naturally with `SaltAIRxClassMembers`, which can then use the same `class_id` to retrieve the drugs that belong to the class. Always check the `status` output before consuming the payload, and remember to parse the JSON string returned in `class_info` if you need field-level access.

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
<tr><td style="word-wrap: break-word;">class_id</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">RxClass ID of the drug class to query. This must be a non-empty string corresponding to a class identifier recognized by the RxNorm API. If this input is empty or only whitespace, the node returns an error status without calling the API; if the ID is not recognized by RxNorm, the API typically returns an error that is propagated in the JSON output.</td><td style="word-wrap: break-word;">100</td></tr>
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
<tr><td style="word-wrap: break-word;">class_info</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">JSON-formatted string containing information about the specified RxClass. The top-level object includes the supplied `class_id` and a `data` field with the RxNorm API response. On failure, the JSON will contain an `error` key describing what went wrong.</td><td style="word-wrap: break-word;">{ "class_id": "100", "data": { "rxclassMinConceptItem": { "classId": "100", "className": "ACE Inhibitors", "classType": "ATC", "classCode": "C09AA" } } }</td></tr>
<tr><td style="word-wrap: break-word;">status</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Human-readable message summarizing the outcome of the request. On success, it confirms that information was retrieved for the given class ID; on validation or API errors, it returns a descriptive message, with API-originated problems prefixed by "API Error:".</td><td style="word-wrap: break-word;">Successfully retrieved RxClass info for class ID 100</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Performance**: Each run triggers a real-time call to the RxNorm API via the RxNorm helper, so latency and throughput depend on the external service; caching results for commonly used class IDs can improve performance.
- **Limitations**: If `class_id` is empty or only whitespace, the node returns "{}" for `class_info` and a validation error `status` without querying the external API, so upstream validation is important.
- **Behavior**: When the RxNorm API indicates an error (such as an unknown class ID or service problem), the node embeds that information in the `class_info` JSON under an `error` field and sets the `status` to start with "API Error:", rather than throwing an exception.
- **Behavior**: The `class_info` output is always a string containing JSON, not a parsed structure; any downstream consumers must parse this string before accessing nested fields like `data` or `rxclassMinConceptItem`.

## Troubleshooting
- **Empty JSON and status 'Error: Class ID cannot be empty'**: The `class_id` field was blank or contained only whitespace. Provide a valid RxClass identifier, for example one returned by `SaltAIRxClassSearch`, and re-run the node.
- **Status begins with 'API Error:' and class_info contains an 'error' field**: The RxNorm API rejected the request or encountered an issue (e.g., invalid ID or temporary outage). Verify the `class_id`, confirm external service availability, and consider adding retry or backoff logic in the workflow.
- **Downstream nodes fail when interpreting class_info**: This usually occurs because the consumer expects a structured object rather than a JSON string. Update the downstream logic to parse the JSON string output before accessing internal fields.
- **Differences in results across runs for the same class_id**: The node always pulls live information from RxNorm, so changes in the underlying terminology can change the results. For reproducible analyses or audits, store the `class_info` payload at execution time instead of recomputing it later.
