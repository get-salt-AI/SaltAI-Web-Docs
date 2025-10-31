# RxNorm Drug Interactions

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Retrieves drug interaction data for one or more RxNorm concept IDs (RxCUIs) based on a selected relationship type. Returns a JSON-formatted string mapping each input RxCUI to its interaction results, along with a status message indicating success or error details.

</div>
<div style="flex: 0 0 300px;"><img src="../../../images/previews/new-uncategorized/SaltAIRxNormDrugInteractions.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you have one or multiple RxCUIs and need to fetch interaction-related information constrained by a specific RxNorm relationship type (e.g., contained_in, has_precise_ingredient). Commonly placed after a search or concept-info node that yields RxCUIs. Provide either a single RxCUI or a JSON array of RxCUIs as a string; select the relationship type to tailor the interaction context.

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
<tr><td style="word-wrap: break-word;">rxcui</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The RxNorm Concept Unique Identifier(s). Accepts a single RxCUI (e.g., "161") or a JSON array string of RxCUIs (e.g., "["161","198440"]"). When an array is provided, the node retrieves results for each RxCUI and aggregates them.</td><td style="word-wrap: break-word;">["161","198440"]</td></tr>
<tr><td style="word-wrap: break-word;">relationship_type</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The RxNorm relationship context to use when fetching interactions. Choose from: boss_of, consists_of, constitutes, contained_in, contains, dose_form_of, doseformgroup_of, form_of, has_boss, has_dose_form, has_doseformgroup, has_form, has_ingredient, has_ingredients, has_part, has_precise_ingredient, has_quantified_form, has_tradename, ingredient_of, ingredients_of, inverse_isa, isa, part_of, precise_ingredient_of, quantified_form_of, reformulated_to, reformulation_of, tradename_of.</td><td style="word-wrap: break-word;">contained_in</td></tr>
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
<tr><td style="word-wrap: break-word;">drug_interactions</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">A JSON-formatted string containing the inputs and interaction results. Structure: { "rxcui": [list of input RxCUIs], "relationship_type": "...", "data": { "<rxcui>": <interaction_result>, ... } }.</td><td style="word-wrap: break-word;">{   "rxcui": ["161"],   "relationship_type": "contained_in",   "data": { "161": { "interactionTypeGroup": [...] } } }</td></tr>
<tr><td style="word-wrap: break-word;">status</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Human-readable status message indicating success or describing any error encountered.</td><td style="word-wrap: break-word;">Successfully retrieved drug interactions for RXCUI ['161'] with relationship type contained_in</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Input flexibility**: Provide a single RxCUI (e.g., "161") or a JSON array string (e.g., "["161","198440"]"). The node detects and handles both.
- **Required input**: An empty rxcui string returns an error and no results.
- **Relationship scope**: Results depend on the selected relationship_type and the underlying RxNorm data availability for that context.
- **Output format**: The node returns JSON strings; downstream nodes expecting structured data should parse the output.
- **Per-RxCUI aggregation**: The data field is a map keyed by each input RxCUI, allowing batch retrieval and consolidated results.
- **External dependency**: Results come from an external RxNorm service via the platform’s RxNorm API; service availability and rate limits can affect results.

## Troubleshooting
- **Empty RXCUI error**: If status reports "Error: RXCUI cannot be empty", ensure the rxcui field contains a non-empty string or a valid JSON array string.
- **Malformed list input**: If providing multiple RxCUIs, ensure the rxcui value is a valid JSON array string (e.g., "["161","198440"]").
- **API error messages**: If status starts with "API Error:", the upstream RxNorm service returned an error. Retry later or verify the RxCUI and relationship_type.
- **Unexpected or empty results**: Some RxCUIs may not have interaction data for the selected relationship_type. Try a different relationship or verify the RxCUI.
- **Slow or partial results with many RxCUIs**: Large input lists can increase latency. Reduce the number of RxCUIs per request if necessary.
- **Downstream parsing issues**: The drug_interactions output is a JSON string. Ensure downstream nodes or scripts parse it to access nested fields.
