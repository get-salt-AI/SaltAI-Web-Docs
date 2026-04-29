# RxNorm Related Concepts

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

This node queries the RxNorm service to find concepts that are related to a specified RxNorm Concept Unique Identifier (RxCUI) via a chosen relationship type (e.g., has_precise_ingredient, ingredient_of). It wraps the RxNormAPI get_related_concepts call and returns a JSON-formatted payload containing the input RxCUI, the relationship type, and the raw API response, along with a human-readable status message. It is designed for exploring clinical, ingredient, and product relationships between drugs in RxNorm.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/healthcare/rxnorm/saltairxnormrelatedconcepts.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you already have an RxCUI (often obtained from the RxNorm Drug Search node or a prior pipeline step) and you need to explore how that concept relates to others in the RxNorm ontology. Common use cases include: expanding a generic ingredient to its precise ingredients or branded products, discovering formulations that contain a given ingredient, or traversing parent/child relationships between clinical drugs and ingredients. In a typical workflow, you might first use "SaltAIRxNormSearch" to resolve a drug name into one or more RxCUIs, optionally inspect details with "SaltAIRxNormConceptInfo", and then feed a chosen RxCUI into "SaltAIRxNormRelatedConcepts" to retrieve related concepts for downstream analysis or UI display. Downstream consumers often include custom logic nodes for filtering or restructuring the JSON, visualization/reporting nodes, or further RxNorm utilities like interaction exploration. Best practice is to use well-known RxNorm relationship labels (such as has_precise_ingredient, contains, ingredient_of, tradename_of) and to validate that the RxCUI is non-empty and valid before calling this node, as the node will return empty JSON and an error status if the RXCUI string is blank.

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
<tr><td style="word-wrap: break-word;">rxcui</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Single RxNorm Concept Unique Identifier (RxCUI) to query for related concepts. Must be a non-empty string of digits corresponding to a valid RxNorm concept; the node checks only that it is non-empty, but invalid or unknown RXCUIs will typically yield an empty or error response from the RxNorm API.</td><td style="word-wrap: break-word;">313</td></tr>
<tr><td style="word-wrap: break-word;">relationship_type</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The RxNorm relationship label to follow from the given RxCUI. This must be a valid RxNorm relationship type understood by the RxNorm API (e.g., has_precise_ingredient, contains, ingredient_of, tradename_of, dose_form_of). The node does not enforce a fixed list here; invalid relationship types will usually cause the underlying API to return an error or empty data.</td><td style="word-wrap: break-word;">has_precise_ingredient</td></tr>
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
<tr><td style="word-wrap: break-word;">related_concepts</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">A JSON-formatted string containing the related concepts result. The top-level structure includes the requested rxcui, the relationship_type, and a data field holding the raw response from RxNormAPI.get_related_concepts. Downstream nodes should parse this string as JSON to iterate related concepts, extract identifiers, or transform for display.</td><td style="word-wrap: break-word;">{   "rxcui": "313",   "relationship_type": "has_precise_ingredient",   "data": {     "relatedGroup": {       "rxcui": "313",       "conceptGroup": [         {           "tty": "SCD",           "conceptProperties": [             {               "rxcui": "198440",               "name": "Acetaminophen 500 MG Oral Tablet",               "tty": "SCD"             }           ]         }       ]     }   } }</td></tr>
<tr><td style="word-wrap: break-word;">status</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Human-readable status message summarizing the operation. On success it indicates that related concepts for the given RXCUI and relationship type were retrieved; on failure it contains an error description (for example, input validation failures or wrapped API error messages).</td><td style="word-wrap: break-word;">Successfully retrieved has_precise_ingredient for RXCUI 313</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Performance**: The node makes a live call to the RxNorm service via RxNormAPI.get_related_concepts; response time depends on network latency and RxNorm availability but is generally acceptable for interactive workflows.
- **Limitations**: If `rxcui` is an empty string, the node short-circuits and returns an empty JSON object ("{}") with an error status; it does not validate that the RxCUI exists in RxNorm, so invalid identifiers may yield empty or API-specific error payloads.
- **Behavior**: The output is always a STRING containing JSON, not a structured object type; downstream nodes must explicitly parse this JSON if they need to access individual fields or iterate related concepts.
- **Behavior**: Any error field returned by the underlying RxNorm API is passed through inside the JSON payload and also reflected in the status message as "API Error: <message>", which is useful for debugging relationship_type or connectivity issues.

## Troubleshooting
- **Common Error 1**: "Error: RXCUI cannot be empty" – This occurs when the `rxcui` input is blank or only whitespace. Ensure you are passing a non-empty RxCUI string, typically obtained from "SaltAIRxNormSearch" or "SaltAIRxCUIByNDC".
- **Common Error 2**: Status contains "API Error: ..." and `related_concepts` JSON includes an `error` field – This generally indicates a problem reported by the RxNorm service, such as an invalid relationship_type or service outage. Verify that the relationship_type is a valid RxNorm relation (e.g., has_precise_ingredient, contains, ingredient_of) and retry later if the service appears unavailable.
- **Common Issue 3**: `related_concepts` is a valid JSON string but `data` is empty or lacks expected fields – This usually means that there are simply no related concepts for the specified RXCUI and relationship_type. Try a different relationship_type or confirm via RxNorm documentation that the relationship exists for this concept.
