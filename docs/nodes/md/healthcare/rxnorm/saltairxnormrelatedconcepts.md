# RxNorm Related Concepts

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Retrieves concepts related to a given RxNorm concept (RxCUI) based on a specified relationship type. The node returns a JSON-formatted string containing the input RxCUI, the relationship type, and the related concept data, along with a status message. It handles basic validation and surfaces API errors as status text.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/healthcare/rxnorm/saltairxnormrelatedconcepts.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need to explore a drug concept's relationships in RxNorm (e.g., ingredients, dose forms, brand relationships). Provide a single RxCUI and a relationship type to fetch the corresponding related concepts. Commonly used after searching for a drug to navigate its hierarchy or map to related concepts.

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
<tr><td style="word-wrap: break-word;">rxcui</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The RxNorm Concept Unique Identifier for the source concept to query. Must be a non-empty string representing a single RxCUI.</td><td style="word-wrap: break-word;">313</td></tr>
<tr><td style="word-wrap: break-word;">relationship_type</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The RxNorm relationship type to use when finding related concepts. Must be a valid RxNorm relationship keyword.</td><td style="word-wrap: break-word;">has_precise_ingredient</td></tr>
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
<tr><td style="word-wrap: break-word;">related_concepts</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Pretty-printed JSON string containing the input RxCUI, the requested relationship type, and the related concept data returned by the RxNorm service.</td><td style="word-wrap: break-word;">{   "rxcui": "313",   "relationship_type": "has_precise_ingredient",   "data": { "...": "..." } }</td></tr>
<tr><td style="word-wrap: break-word;">status</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Human-readable status message indicating success or describing an error encountered during the request.</td><td style="word-wrap: break-word;">Successfully retrieved has_precise_ingredient for RXCUI 313</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Input validation**: The rxcui input must be a non-empty string; empty values return an error.
- **Relationship types**: The node does not validate relationship_type values; invalid or unsupported types will result in API errors surfaced in the status output.
- **Output format**: related_concepts is a JSON string, not a parsed object. Downstream nodes expecting structured data may need to parse it.
- **Single concept**: This node accepts a single RxCUI, not a list.
- **Error propagation**: If the underlying service returns an error, the node returns "{}" for related_concepts and an error message in status.

## Troubleshooting
- **Empty output with error status**: Verify rxcui is non-empty and a valid RxNorm identifier.
- **API Error in status**: Check that relationship_type is a valid RxNorm relationship (e.g., has_ingredient, has_precise_ingredient, dose_form_of, tradename_of). Correct the value and retry.
- **Unexpected data structure in related_concepts**: The output is a JSON string. Ensure downstream steps parse the string before accessing fields.
- **No related concepts found**: The RxCUI may not have relations of the specified type. Try a different relationship_type or confirm the RxCUI's scope in RxNorm.
