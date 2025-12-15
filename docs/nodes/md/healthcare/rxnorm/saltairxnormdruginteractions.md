# RxNorm Drug Interactions

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Retrieves drug interaction data for one or more RxNorm concept identifiers (RxCUI) and a specified relationship type. Returns a JSON-formatted summary and a status message. Accepts a single RxCUI or a JSON array of RxCUIs and aggregates results.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/healthcare/rxnorm/saltairxnormdruginteractions.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need to look up potential drug interactions or related relationship data for specific RxNorm concepts. It fits into medication analysis or safety workflows, often after obtaining RxCUIs from search or concept nodes. You can query multiple RxCUIs at once and select the relationship type to explore.

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
<tr><td style="word-wrap: break-word;">rxcui</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">RxNorm Concept Unique Identifier(s). Provide a single RxCUI as a plain string (e.g., "161") or multiple as a JSON array string (e.g., "[161, 198440]").</td><td style="word-wrap: break-word;">[161]</td></tr>
<tr><td style="word-wrap: break-word;">relationship_type</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The RxNorm relationship type to query for interactions/relations. Must be one of: boss_of, consists_of, constitutes, contained_in, contains, dose_form_of, doseformgroup_of, form_of, has_boss, has_dose_form, has_doseformgroup, has_form, has_ingredient, has_ingredients, has_part, has_precise_ingredient, has_quantified_form, has_tradename, ingredient_of, ingredients_of, inverse_isa, isa, part_of, precise_ingredient_of, quantified_form_of, reformulated_to, reformulation_of, tradename_of.</td><td style="word-wrap: break-word;">contained_in</td></tr>
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
<tr><td style="word-wrap: break-word;">drug_interactions</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">JSON string containing the inputs and fetched interaction data. Structure: { "rxcui": [list_of_rxcui_strings], "relationship_type": "...", "data": { "<rxcui>": <interaction_result>, ... } }.</td><td style="word-wrap: break-word;">{   "rxcui": ["161"],   "relationship_type": "contained_in",   "data": {     "161": { /* interaction/relationship payload from service */ }   } }</td></tr>
<tr><td style="word-wrap: break-word;">status</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Human-readable status message indicating success or the nature of any error.</td><td style="word-wrap: break-word;">Successfully retrieved drug interactions for RXCUI ['161'] with relationship type contained_in</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Input format**: Provide multiple RxCUIs as a JSON array string (e.g., "[161, 198440]"). A single RxCUI can be provided without brackets (e.g., "161").
- **Required inputs**: Both rxcui and relationship_type are required; an empty rxcui returns an error.
- **Relationship types**: Only the listed relationship types are supported; choose from the provided options.
- **Output structure**: Results aggregate per RxCUI under the data field and include the original rxcui list and relationship_type for clarity.
- **API dependency**: This node relies on an internal RxNorm service; any service issues will be reflected in the returned status and payload.

## Troubleshooting
- **Empty RXCUI error**: If status reads "Error: RXCUI cannot be empty", ensure rxcui is a non-empty string or a valid JSON array string.
- **Malformed array input**: If providing multiple RXCUIs, ensure the rxcui input is a valid JSON array string (e.g., "["161", "198440"]" or "[161, 198440]").
- **API Error**: If status contains "API Error: ...", the underlying service returned an issue. Retry later or verify the rxcui and relationship_type.
- **Unexpected results**: Verify you selected the intended relationship_type. Different types return different relationship data.
- **No data returned**: Interaction or relationship data may not exist for the given RxCUI/type. Try alternate relationship types or validate the RxCUI.
