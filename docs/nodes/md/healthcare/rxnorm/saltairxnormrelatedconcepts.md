# RxNorm Related Concepts

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Retrieves RxNorm concepts that are related to a given RxCUI by a specified relationship type. It queries the RxNorm service and returns a structured JSON string of related concepts along with a status message. Typical relationships include ingredient, brand/generic, and formulation linkages.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/healthcare/rxnorm/saltairxnormrelatedconcepts.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you have an RxCUI (e.g., from a prior RxNorm search) and need to explore related concepts such as precise ingredients, brand names, or other clinical relationships. Common workflows include: searching for a drug to get an RxCUI, then using this node to expand to brand/generic variants or formulations for downstream analysis or display.

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
<tr><td style="word-wrap: break-word;">rxcui</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The RxNorm Concept Unique Identifier of the starting concept to explore relations from.</td><td style="word-wrap: break-word;">313</td></tr>
<tr><td style="word-wrap: break-word;">relationship_type</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The RxNorm relationship type to filter related concepts by. Examples include 'has_precise_ingredient' and 'has_brand_name'.</td><td style="word-wrap: break-word;">has_precise_ingredient</td></tr>
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
<tr><td style="word-wrap: break-word;">related_concepts</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">A JSON-formatted string containing the related concepts returned by the RxNorm service for the given RxCUI and relationship type.</td><td style="word-wrap: break-word;">{ "rxcui": "313", "relationship_type": "has_precise_ingredient", "relatedConcepts": [ { "rxcui": "12345", "name": "Example Drug", "tty": "SCD" } ] }</td></tr>
<tr><td style="word-wrap: break-word;">status</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">A human-readable message indicating success or describing any encountered error.</td><td style="word-wrap: break-word;">Successfully retrieved related concepts for RXCUI 313</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Relationship types**: Valid values must be supported by RxNorm (e.g., 'has_precise_ingredient', 'has_brand_name'). Unsupported values will return empty or error responses.
- **Input format**: This node expects a single RxCUI as a string. Ensure the input is not empty and is a valid RxNorm identifier.
- **Output structure**: The related_concepts output is a JSON-formatted STRING; downstream nodes should parse it before further processing.
- **External dependency**: Results depend on the availability and response of the RxNorm REST service; transient network or service issues can affect output.

## Troubleshooting
- **Empty results**: Verify the RxCUI is correct and the relationship_type is valid for that RxCUI. Try alternative relationship types such as 'has_brand_name' or 'has_precise_ingredient'.
- **API error message in status**: Indicates a remote service or network issue. Retry later, and ensure your environment allows outbound HTTPS requests to the RxNorm API.
- **Malformed JSON downstream**: Remember the first output is a STRING containing JSON; parse it before attempting to access fields.
- **Unexpected relationships**: Different RxCUIs support different relationship types. Confirm the concept type (ingredient, brand, clinical drug) and adjust the relationship_type accordingly.
