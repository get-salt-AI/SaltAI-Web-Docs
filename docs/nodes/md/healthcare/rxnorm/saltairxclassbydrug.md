# Drug Classes

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

This node queries the RxNorm RxClass API to fetch all drug classes associated with a specific RxNorm Concept Unique Identifier (RXCUI). It can be scoped to a particular classification system or configured to return results across systems. The node wraps the raw API response into a JSON structure that is convenient for downstream analysis or visualization.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/healthcare/rxnorm/saltairxclassbydrug.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you already have an RxNorm RXCUI for a drug and need standardized drug class information. Typical scenarios include building therapeutic class views, powering clinical decision support tools, and constructing cohorts based on drug classes. In a workflow, it generally follows a node or process that normalizes drug identifiers (such as names or NDC codes) into RXCUIs, and precedes nodes that operate on class-level concepts, such as population analysis or mechanism-of-action summaries. A common pattern is: resolve a drug to RXCUI, call this "Drug Classes" node to retrieve its classifications, then feed the `drug_classes` JSON into nodes like `SaltAIDrugClassAnalysis` for enriched analysis or `SaltAIRxClassDrugMembers` to list all drugs in a chosen class. For large-scale or batch workflows, prefer specifying a single `classification_system` aligned with your analytic standard (for example ATC or MEDRT) rather than requesting all systems, to reduce response size and latency.

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
<tr><td style="word-wrap: break-word;">rxcui</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The RxNorm Concept Unique Identifier (RXCUI) for the drug of interest. Must be a non-empty string; whitespace-only values are treated as invalid and cause the node to return an error without calling the external API. While any string is accepted syntactically, only valid RXCUIs will produce meaningful class data; invalid or obsolete RXCUIs result in error payloads from the RxClass service.</td><td style="word-wrap: break-word;">83367</td></tr>
<tr><td style="word-wrap: break-word;">classification_system</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The classification source or relationship system to filter results by. The allowed values are provided by RxNormAPI.drug_class_source_relations() and typically include identifiers such as "ATC", "MEDRT", "VA", "SNOMEDCT", "RXNORM", "CDC", and an option representing all systems (for example "ALL"). Choose a specific system to keep responses focused, or use an all-systems-style option when you need classifications across multiple taxonomies.</td><td style="word-wrap: break-word;">ATC</td></tr>
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
<tr><td style="word-wrap: break-word;">drug_classes</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">A JSON-formatted string containing the class information returned by the RxClass getClassesByRxNormDrugId endpoint. The top-level object includes the input `rxcui`, the selected `classification_system`, and a `classes` field that holds the raw RxClass response. After parsing, you can inspect fields such as `rxclassDrugInfoList` and its nested `rxclassDrugInfo` entries to obtain specific class IDs, names, and types for the drug.</td><td style="word-wrap: break-word;">{   "rxcui": "83367",   "classification_system": "ATC",   "classes": {     "rxclassDrugInfoList": {       "rxclassDrugInfo": [         {           "rxclassMinConceptItem": {             "classId": "C10AA05",             "className": "HMG CoA reductase inhibitors",             "classType": "ATC"           }         }       ]     }   } }</td></tr>
<tr><td style="word-wrap: break-word;">status</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">A human-readable status message summarizing the outcome. On success it confirms that drug classes were successfully retrieved for the specified RXCUI. On failure it contains an error description, such as an input validation error (`Error: RXCUI cannot be empty`) or a message prefixed with `API Error:` derived from the RxClass service response.</td><td style="word-wrap: break-word;">Successfully retrieved drug classes for RXCUI 83367</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Performance**: Requesting classes from all available systems (for example via an "ALL"-style option) can produce large responses; when feasible, restrict `classification_system` to a single taxonomy like ATC or MEDRT to keep payloads smaller and faster to process.
- **Limitations**: The node does not independently validate RXCUI correctness; invalid, retired, or unsupported RXCUIs generally return error objects from RxClass, which are passed through in the `drug_classes` JSON rather than being filtered or transformed.
- **Behavior**: If `rxcui` is empty or only whitespace, the node skips the external API call and returns `{}` as `drug_classes` with the status message `Error: RXCUI cannot be empty`.
- **Behavior**: When RxClass returns an error, the node pretty-prints that error JSON into `drug_classes` and sets `status` to start with `API Error:`. Downstream consumers should check for an `error` key or inspect the status before assuming a normal class structure.
- **Integration**: The node relies on RxNormAPI.get_classes_by_rxnorm_drug_id; any network issues, rate limits, or service outages at the RxClass endpoint will surface as API error content in the outputs rather than partial or silently truncated results.

## Troubleshooting
- **Empty RXCUI input**: Symptom: `drug_classes` is `{}` and `status` equals `Error: RXCUI cannot be empty`. Resolution: Ensure the upstream node or user input provides a non-empty RXCUI string and remove any leading or trailing whitespace before invoking this node.
- **API Error response**: Symptom: `status` begins with `API Error:` and the `drug_classes` JSON contains an `error` field instead of `rxclassDrugInfoList`. Resolution: Confirm that the RXCUI is valid in RxNorm and that `classification_system` is one of the allowed values from RxNormAPI.drug_class_source_relations(); if both are valid, check for external service downtime or throttle limits.
- **Unexpected JSON structure**: Symptom: Downstream processing fails when trying to access `rxclassDrugInfoList` or `rxclassDrugInfo`. Resolution: Parse `drug_classes` and explicitly check for an `error` key before traversing nested fields; handle error payloads separately from successful responses.
- **No classes returned**: Symptom: Status indicates success, but `classes` is empty or missing expected systems. Resolution: Try changing `classification_system` to another supported value or to an all-systems option, because not every drug is classified in every taxonomy covered by RxClass.
