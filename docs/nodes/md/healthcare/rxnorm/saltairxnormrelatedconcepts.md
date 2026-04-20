# RxNorm Related Concepts

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

This node queries the RxNorm service to retrieve concepts related to a specified RxNorm Concept Unique Identifier (RxCUI) based on a chosen relationship type such as ingredient, dose form, or tradename relationships. It wraps the RxNormAPI get_related_concepts call and returns a structured JSON payload plus a human-readable status message. Use it to navigate the RxNorm relationship graph and explore how a drug concept connects to ingredients, dose forms, tradenames, and other semantic links.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/healthcare/rxnorm/saltairxnormrelatedconcepts.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you already have an RxCUI and you need to explore its semantic neighborhood in RxNorm, such as finding precise ingredients, related dose forms, or other related drug concepts. It typically appears downstream of nodes that produce or resolve RxCUIs, such as `SaltAIRxNormSearch`, `SaltAIRxNormConceptInfo`, or `SaltAIRxCUIByNDC`. Connect the RxCUI string output from those nodes into this node's `rxcui` input, and specify the relationship type you want to traverse (for example, `has_precise_ingredient`, `has_dose_form`, or `tradename_of`). The node returns a JSON-formatted string that contains the original RxCUI, the requested relationship type, and the raw related-concepts data returned by the RxNorm API, along with a `status` output summarizing success or failure. In larger workflows, feed `related_concepts` into downstream analysis nodes (for filtering, aggregation, or visualization) or into LLM-powered interpretation nodes to generate clinical summaries or mappings.

Recommended positions and patterns:
1) Upstream: use `SaltAIRxNormSearch` to find a concept by drug name, or `SaltAIRxCUIByNDC` to resolve from an NDC, then pass the RxCUI here.
2) Mid-pipeline: use `SaltAIRxNormRelatedConcepts` repeatedly with different relationship types to explore multiple aspects of the same drug (e.g., ingredients and tradenames).
3) Downstream: connect to custom analytics nodes or report-generation nodes that can parse the JSON and present lists of related ingredients, brands, or dose forms.

Best practices include standardizing on a set of relationship types for a given analysis, validating RxCUIs first using `SaltAIRxNormConceptInfo`, and avoiding unnecessary repeated calls by caching results where appropriate in larger pipelines.

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
<tr><td style="word-wrap: break-word;">rxcui</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">RxNorm Concept Unique Identifier (RxCUI) for the drug or concept to explore. Must be a non-empty numeric string that RxNorm recognizes as a valid concept identifier. The node accepts a single RxCUI string, not a JSON array; if blank or whitespace-only, it returns an error without calling the external service.</td><td style="word-wrap: break-word;">313</td></tr>
<tr><td style="word-wrap: break-word;">relationship_type</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The relationship type to follow when fetching related concepts from RxNorm. Must be a valid RxNorm relationship string supported by the underlying RxNormAPI, such as `has_precise_ingredient`, `has_dose_form`, `tradename_of`, `contains`, or similar. If an invalid or unsupported relationship is provided, the RxNorm API may return an error object, which this node passes through in the output JSON.</td><td style="word-wrap: break-word;">has_precise_ingredient</td></tr>
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
<tr><td style="word-wrap: break-word;">related_concepts</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">A JSON-formatted string containing related concepts returned by RxNorm for the specified RxCUI and relationship type. The structure includes the original `rxcui`, the `relationship_type`, and a `data` field with the raw payload from RxNorm (typically including a `relatedGroup` with nested concept groups and concept properties like related RxCUIs, names, and term types). This string is intended for further parsing, filtering, or summarization by downstream nodes.</td><td style="word-wrap: break-word;">{   "rxcui": "313",   "relationship_type": "has_precise_ingredient",   "data": {     "relatedGroup": {       "rxcui": "313",       "conceptGroup": [         {           "tty": "PIN",           "conceptProperties": [             {               "rxcui": "161",               "name": "Acetaminophen 500 MG Oral Tablet",               "synonym": "acetaminophen 500 mg tablet",               "tty": "SCD"             }           ]         }       ]     }   } }</td></tr>
<tr><td style="word-wrap: break-word;">status</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">A human-readable status message summarizing the result of the operation. On success, it confirms that related concepts were retrieved for the given RxCUI and relationship type. On failure, it reports either an input validation error (such as an empty RxCUI) or describes an API error from the underlying RxNorm service.</td><td style="word-wrap: break-word;">Successfully retrieved has_precise_ingredient for RXCUI 313</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Performance**: Each execution triggers a call to the external RxNorm API via RxNormAPI.get_related_concepts, so end-to-end latency depends on network conditions and the responsiveness of the RxNorm service; avoid unnecessary high-frequency or large-scale loops without batching or caching.
- **Limitations**: The node is designed for a single RxCUI per invocation; if you need related concepts for many RxCUIs, you must iterate over them or create a higher-level workflow that orchestrates repeated calls.
- **Behavior**: If the `rxcui` input is empty or only whitespace, the node immediately returns an empty JSON object (`"{}"`) as `related_concepts` and a descriptive error in `status`, without making any external API call.
- **Behavior**: When the underlying RxNormAPI response includes an `error` field, the node serializes this error into the `related_concepts` output and sets `status` to an `API Error:` message; it does not attempt retries or automatic correction.
- **Limitations**: The exact structure of the `data` section in the returned JSON is determined by the RxNorm service and can change over time; downstream consumers should defensively handle missing fields, additional attributes, or empty concept lists.

## Troubleshooting
- **Empty output with validation error**: If `related_concepts` is `{}` and `status` is `Error: RXCUI cannot be empty`, check that the `rxcui` input is a non-empty string containing a valid numeric identifier and that it is not accidentally being passed an empty variable.
- **API Error status**: If `status` starts with `API Error:` and `related_concepts` contains an error object, the RxNorm service has rejected the request (for example, invalid RxCUI or unsupported relationship type). Verify the RxCUI exists (using `SaltAIRxNormConceptInfo` or the RxNorm browser) and check that `relationship_type` is a valid RxNorm relationship string, then retry.
- **Unexpected JSON structure downstream**: If parsing fails in downstream nodes, inspect the raw `related_concepts` string to confirm the structure under `data`. Update your parsing logic to account for possibly missing `conceptGroup` arrays or empty lists when there are no related concepts.
- **No related concepts found**: If the call succeeds but `data` contains no related concepts, it may indicate that there are no relationships of the specified type for that RxCUI. Try another relationship type (for example, switch from `has_precise_ingredient` to `has_dose_form` or `tradename_of`) to verify whether relationships exist.
