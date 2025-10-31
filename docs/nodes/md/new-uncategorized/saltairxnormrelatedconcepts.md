# RxNorm Related Concepts

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Fetches concepts related to a given RxNorm concept (RxCUI) by a specified relationship type. Returns a JSON-formatted string containing the input RXCUI, the relationship type used, and the related concept data, along with a status message describing the outcome.

</div>
<div style="flex: 0 0 300px;"><img src="../../../images/previews/new-uncategorized/saltairxnormrelatedconcepts.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you have an RxCUI and need to explore its relationships (e.g., ingredients, brand/tradename, dose form) to other RxNorm concepts. Typical workflow: search or look up an RxCUI, then supply it here with a relationship type to expand or navigate the concept graph for analysis or downstream filtering.

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
<tr><td style="word-wrap: break-word;">rxcui</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The RxNorm Concept Unique Identifier to query. Must be a single RxCUI string.</td><td style="word-wrap: break-word;">313</td></tr>
<tr><td style="word-wrap: break-word;">relationship_type</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The relationship to follow from the given RxCUI. Examples include has_precise_ingredient, contains, ingredient_of, dose_form_of, tradename_of, etc. Must be a valid RxNorm relationship type.</td><td style="word-wrap: break-word;">has_precise_ingredient</td></tr>
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
<tr><td style="word-wrap: break-word;">related_concepts</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">JSON-formatted string with the RXCUI, the relationship type, and the related concept data returned by the RxNorm service.</td><td style="word-wrap: break-word;">{   "rxcui": "313",   "relationship_type": "has_precise_ingredient",   "data": { "relatedGroup": { /* ... RxNorm response ... */ } } }</td></tr>
<tr><td style="word-wrap: break-word;">status</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Human-readable message indicating success or the type of error encountered.</td><td style="word-wrap: break-word;">Successfully retrieved has_precise_ingredient for RXCUI 313</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Input validation**: An empty RXCUI returns an error; ensure the RXCUI is a non-empty string.
- **Relationship types**: Only valid RxNorm relationship types will return data. Invalid values typically result in an API error message in the output.
- **Output format**: The related_concepts output is a JSON string; parse it downstream if you need to access fields programmatically.
- **Error propagation**: If the underlying service reports an error, the error details are included within the JSON output and reflected in the status.
- **Performance**: Large or complex relationships may return sizable JSON responses; plan downstream handling accordingly.

## Troubleshooting
- **Empty output or '{}'**: Verify the RXCUI is correct and not empty. Some concepts may not have results for the chosen relationship type.
- **API Error in status**: Check the relationship_type spelling and that it is supported by RxNorm. Retry later if the service is temporarily unavailable.
- **JSON parsing failures downstream**: Ensure you are parsing the related_concepts output as JSON text. If your tool expects an object, convert the string to an object first.
- **Unexpected data shape**: Relationship responses can vary by type. Inspect the JSON structure to confirm paths before assuming specific fields.
