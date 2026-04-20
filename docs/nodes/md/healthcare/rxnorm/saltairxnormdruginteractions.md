# RxNorm Drug Interactions

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

This node queries the RxNorm API for relationship-based drug interaction data for one or more RxNorm CUIs (concept identifiers). It supports a wide range of RxNorm relationship types (such as contains, ingredient_of, tradename_of) and packages the responses into a structured JSON payload plus a human-readable status message. Input is validated for emptiness, and API or processing issues are surfaced through the status output while still preserving a JSON result string.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/healthcare/rxnorm/saltairxnormdruginteractions.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need to understand how drugs are related in the RxNorm ontology, for example to determine ingredients, dose forms, tradenames, or containment relationships among medications. This is useful for clinical decision support, medication normalization pipelines, and population-level medication analytics where drug relationship information informs grouping, deduplication, or risk analysis.

In a typical workflow, an upstream node like "SaltAIRxNormSearch" (RxNorm Drug Search), "SaltAIRxNormApproximateMatch" (RxNorm Approximate Match), or "SaltAIRxNormConceptInfo" (RxNorm Concept Info) is used to discover or normalize medications and produce RxCUIs. Feed those RxCUIs into the `rxcui` input of this node, optionally as a JSON array string if you need to query multiple concepts in one step. Choose an appropriate `relationship_type` (for example, `has_ingredient` to see which ingredients a branded product contains, or `tradename_of` to map between brand and generic). The node then returns a JSON string with all interaction/relationship data, which can be consumed by downstream analysis nodes, reporting nodes, or custom logic that classifies or filters drugs.

For integration patterns, consider: (1) using this node right after drug identification to enrich each medication with its ingredients or tradename mapping; (2) inserting it into a cohort analysis pipeline before "SaltAIPopulationMedicationAnalysis" to precompute relationship data; and (3) chaining it with custom nodes that parse the JSON and generate summaries, clinical rules, or visualization-ready data. When working with large numbers of RxCUIs, prefer batching into manageable chunks to balance performance and API latency.

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
<tr><td style="word-wrap: break-word;">rxcui</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">One or more RxNorm Concept Unique Identifiers (RxCUIs) to query. This input accepts either a single RxCUI as a plain string (e.g., "161") or a JSON array encoded as a string (e.g., "[161, 857005]"). Whitespace is stripped; if the resulting string is empty the node returns an error. If the first character is '[' and the last is ']', the value is parsed as JSON to obtain a list of RxCUIs; otherwise it is treated as a single RxCUI and internally wrapped into a one-element list. Use numeric RXCUI values as strings and ensure any JSON array is syntactically valid.</td><td style="word-wrap: break-word;">[161, 857005]</td></tr>
<tr><td style="word-wrap: break-word;">relationship_type</td><td>True</td><td style="word-wrap: break-word;">STRING (enum)</td><td style="word-wrap: break-word;">The RxNorm relationship type that defines the kind of interaction or linkage you want to retrieve for the given RxCUIs. Must be one of the supported values: "boss_of", "consists_of", "constitutes", "contained_in", "contains", "dose_form_of", "doseformgroup_of", "form_of", "has_boss", "has_dose_form", "has_doseformgroup", "has_form", "has_ingredient", "has_ingredients", "has_part", "has_precise_ingredient", "has_quantified_form", "has_tradename", "ingredient_of", "ingredients_of", "inverse_isa", "isa", "part_of", "precise_ingredient_of", "quantified_form_of", "reformulated_to", "reformulation_of", "tradename_of". The selected value is passed to the RxNorm API and directly determines what relationships are returned.</td><td style="word-wrap: break-word;">has_ingredient</td></tr>
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
<tr><td style="word-wrap: break-word;">drug_interactions</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">A JSON-formatted string containing the aggregated interaction/relationship results for all requested RxCUIs. The structure is an object with three main keys: "rxcui" (an array of the RxCUIs that were queried, as strings), "relationship_type" (the chosen relationship type), and "data" (a dictionary mapping each RxCUI to the raw interaction/relationship payload returned by the RxNorm API). If the underlying API wrapper returns an error, an "error" field may appear in this JSON, allowing downstream nodes to inspect the cause.</td><td style="word-wrap: break-word;">{   "rxcui": ["161", "857005"],   "relationship_type": "has_ingredient",   "data": {     "161": {       "interactionTypeGroup": [         {           "sourceName": "RXNORM",           "interactionType": [             {               "comment": "Interacts with other opioid analgesics.",               "interactionPair": [                 {                   "interactionConcept": [                     { "minConceptItem": { "rxcui": "857005", "name": "Example Drug", "tty": "SCD" } }                   ]                 }               ]             }           ]         }       ]     },     "857005": {       "interactionTypeGroup": []     }   } }</td></tr>
<tr><td style="word-wrap: break-word;">status</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">A human-readable status string summarizing the outcome of the call. On success, it confirms that drug interactions were retrieved and includes the RxCUIs and relationship_type used. On validation or runtime failures, it contains an explanatory error message such as "Error: RXCUI cannot be empty", "API Error: <underlying message>", or "Error getting drug interactions: <exception details>". Use this output for control flow and user feedback in workflows.</td><td style="word-wrap: break-word;">Successfully retrieved drug interactions for RXCUI ['161', '857005'] with relationship type has_ingredient</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Performance**: The node iterates over each RxCUI and calls the RxNorm API individually, so latency increases with the size of the RxCUI list; for very large lists, consider batching requests or limiting the number of concepts per run.
- **Limitations**: The node does not enforce clinical validity or recency of RxCUIs; it simply forwards each identifier to RxNorm, which may return empty or minimal data for deprecated or uncommon concepts.
- **Behavior**: If the `rxcui` string begins with '[' and ends with ']', it is parsed as JSON; malformed JSON (e.g., missing brackets or invalid syntax) will cause an exception, producing an "Error getting drug interactions" status and an empty `{}` result string.
- **Behavior**: If the aggregated results object contains an "error" key from the API layer, the node returns that as a JSON string and sets the status to `API Error: <error message>`, even if some individual RxCUIs may have partial data, so downstream logic should inspect the JSON before discarding it.

## Troubleshooting
- **Empty RXCUI input**: If `drug_interactions` is `{}` and `status` is `Error: RXCUI cannot be empty`, verify that the `rxcui` field is not blank or just whitespace and that upstream nodes are passing a non-empty string.
- **Malformed JSON array**: If `status` starts with `Error getting drug interactions:` and the message mentions JSON or parsing, check that `rxcui` is a valid JSON array string (for example, "[161, 857005]"), or simplify to a single RxCUI like "161".
- **Unexpected API Error**: When `status` begins with `API Error:` and `drug_interactions` contains an `error` field, inspect that field to see if the issue is an unsupported relationship_type, a service outage, or an invalid RxCUI; retry with a different relationship_type or at a later time if it appears transient.
- **No data for specific RxCUIs**: If the JSON `data` object includes certain RxCUIs with empty arrays or objects, this usually means that RxNorm has no relationships of the requested type for those concepts; try alternative relationship types such as `isa`, `has_ingredient`, or `contained_in` to see if different links are available.
