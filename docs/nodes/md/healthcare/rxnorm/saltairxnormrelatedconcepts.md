# RxNorm Related Concepts

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Retrieves RxNorm concepts related to a specified RxCUI by a chosen relationship type. It queries the RxNorm service and returns a formatted JSON string that includes the input RXCUI, the relationship type, and the related concept data, along with a status message indicating success or error.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/healthcare/rxnorm/saltairxnormrelatedconcepts.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node after you have identified an RxCUI (e.g., from a drug search or mapping by NDC) to explore related concepts such as ingredients, precise ingredients, dose forms, or brand/trade relationships. Typical workflow: obtain an RxCUI -> pass it here with a relationship type (e.g., has_precise_ingredient) -> consume the returned JSON to drive downstream selection, filtering, or display.

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
<tr><td style="word-wrap: break-word;">rxcui</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The RxNorm Concept Unique Identifier to query for related concepts.</td><td style="word-wrap: break-word;">313</td></tr>
<tr><td style="word-wrap: break-word;">relationship_type</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The RxNorm relationship to follow from the given RxCUI. Common values include has_precise_ingredient, has_ingredient, ingredient_of, form_of, dose_form_of, contains, contained_in, has_tradename, tradename_of, isa, inverse_isa.</td><td style="word-wrap: break-word;">has_precise_ingredient</td></tr>
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
<tr><td style="word-wrap: break-word;">related_concepts</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">A JSON string containing the RXCUI, the requested relationship_type, and the related concept data returned by the RxNorm service.</td><td style="word-wrap: break-word;">{   "rxcui": "313",   "relationship_type": "has_precise_ingredient",   "data": { "relatedGroup": { "conceptGroup": [ ... ] } } }</td></tr>
<tr><td style="word-wrap: break-word;">status</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">A human-readable status message describing success or the encountered error.</td><td style="word-wrap: break-word;">Successfully retrieved has_precise_ingredient for RXCUI 313</td></tr>
</tbody>
</table>
</div>

## Important Notes
- Ensure rxcui is a non-empty string; the node returns an error if it is empty.
- relationship_type must be valid for the RxNorm API. Invalid types result in an API Error message in the status and error details in the JSON.
- Output is a single JSON string intended for programmatic parsing; it includes keys: rxcui, relationship_type, and data.
- The node does not validate relationship types locally; it forwards them to the RxNorm service.
- On API or network errors, the node returns a JSON string with the error details and a status message describing the error.

## Troubleshooting
- If status contains 'Error: RXCUI cannot be empty', provide a valid RxCUI string (e.g., from a prior search node).
- If status shows 'API Error', verify the relationship_type value is supported by RxNorm and that the RxCUI exists.
- If the related_concepts JSON is empty or missing expected groups, try a different relationship_type (e.g., has_ingredient vs has_precise_ingredient).
- If rate limits or intermittent failures occur, retry after a short delay or reduce request frequency.
- If the input rxcui is a list or JSON array, pass a single RxCUI string; this node expects a single identifier.
