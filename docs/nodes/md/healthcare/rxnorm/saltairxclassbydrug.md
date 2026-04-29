# Drug Classes

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

This node queries RxClass via the RxNorm API to fetch drug classes associated with a specified RxNorm Concept Unique Identifier (RXCUI). It can limit the results to a selected classification system or request all supported systems. The node returns a JSON string wrapping the RXCUI, the chosen classification system, the raw class data, and a status message.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/healthcare/rxnorm/saltairxclassbydrug.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you already have an RXCUI and need to understand which drug classes it belongs to across one or more classification systems (for example, ATC or VA) for formulary grouping, therapeutic categorization, or decision-support rules. It typically sits downstream of identifier-resolution nodes that map drug names or NDCs to RXCUIs, such as `SaltAIRxCUIByNDC` or a name-based RxNorm lookup node. A common pattern is: (1) resolve a drug name or NDC to RXCUI, (2) feed that RXCUI into `SaltAIRxClassByDrug` with an appropriate `classification_system`, and (3) pass the `drug_classes` JSON to analytics, reporting, or workflow nodes, or use it alongside `SaltAIDrugClassAnalysis` for deeper class and mechanism-of-action analysis. For robust workflows, always inspect the `status` output and be prepared to handle empty or error-bearing responses, especially when running batch jobs.

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
<tr><td style="word-wrap: break-word;">rxcui</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">RxNorm Concept Unique Identifier for the drug whose classes you want to retrieve. Must be a non-empty string (typically numeric) corresponding to a valid RxNorm concept. If this value is empty or consists only of whitespace, the node will not call the external API and instead returns an empty JSON object with an error status.</td><td style="word-wrap: break-word;">83367</td></tr>
<tr><td style="word-wrap: break-word;">classification_system</td><td>True</td><td style="word-wrap: break-word;">CHOICE</td><td style="word-wrap: break-word;">Drug classification system filter used when calling getClassesByRxNormDrugId. Allowed values come from RxNormAPI.drug_class_source_relations() and generally include systems such as ATC, VA, MEDRT, and others, plus an option like ALL to retrieve classes across all systems. Choose a specific system when downstream processes expect that ontology; choose ALL for broad or exploratory analysis.</td><td style="word-wrap: break-word;">ATC</td></tr>
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
<tr><td style="word-wrap: break-word;">drug_classes</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">JSON-formatted string containing the classes associated with the given RXCUI. The structure includes the input RXCUI, the selected classification_system, and the raw class data returned by getClassesByRxNormDrugId. Downstream nodes should parse this string into JSON to inspect fields such as class identifiers, names, and types.</td><td style="word-wrap: break-word;">{   "rxcui": "83367",   "classification_system": "ATC",   "classes": {     "rxclassDrugInfoList": {       "rxclassDrugInfo": [         {           "rxclassMinConceptItem": {             "classId": "C10AA05",             "className": "HMG CoA reductase inhibitors",             "classType": "ATC"           },           "minConcept": {             "rxcui": "83367",             "name": "atorvastatin 10 MG Oral Tablet"           }         }       ]     }   } }</td></tr>
<tr><td style="word-wrap: break-word;">status</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Human-readable status message summarizing the result. On success, it confirms that drug classes were retrieved for the specified RXCUI. On failure, it contains an explanatory error message, including validation errors (such as empty RXCUI) or errors propagated from the RxClass/RxNorm API.</td><td style="word-wrap: break-word;">Successfully retrieved drug classes for RXCUI 83367</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Performance**: Each execution makes an HTTP call to the external RxClass/RxNorm service. For high-volume workloads, consider caching frequent RXCUI lookups, batching operations, or adding rate limiting to avoid latency spikes or hitting external limits.
- **Limitations**: Even when the call succeeds, some RXCUIs may have no mapped classes in the chosen classification_system, resulting in an empty or minimal `classes` structure. This reflects terminology coverage, not necessarily an error.
- **Behavior**: If `rxcui` is empty or whitespace-only, the node skips the API call and returns "{}" for `drug_classes` with the status "Error: RXCUI cannot be empty".
- **Behavior**: When the external API reports an issue (for example, invalid parameters, network failures, or server errors), the returned JSON includes an `error` field and the `status` output begins with "API Error:", which downstream consumers should detect before using the class data.

## Troubleshooting
- **Empty RXCUI input**: Symptom: `drug_classes` is "{}" and `status` is "Error: RXCUI cannot be empty". Resolution: Ensure an upstream node produces a valid, non-empty RXCUI string and that it is correctly wired to this node’s `rxcui` input.
- **API Error from RxClass/RxNorm**: Symptom: `status` starts with "API Error:" and the `drug_classes` JSON contains an `error` entry. Resolution: Confirm that `classification_system` is one of the supported options, verify the RXCUI, and check the availability of the external service. Retry if the problem may be transient.
- **No classes returned**: Symptom: Status indicates success, but the `classes` structure is empty or lacks expected rxclassDrugInfo entries. Resolution: Verify that the RXCUI corresponds to a concept that has class mappings in the selected system. If needed, rerun with ALL or another system (for example, VA instead of ATC) to see if mappings exist elsewhere.
- **Downstream JSON parsing issues**: Symptom: Nodes consuming `drug_classes` fail due to type or key errors. Resolution: Make sure downstream logic parses `drug_classes` from a string into JSON before accessing fields, and handle cases where an `error` field is present or where expected lists like `rxclassDrugInfoList` are missing.
