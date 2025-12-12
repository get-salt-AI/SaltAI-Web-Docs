# RxNorm Drug Interactions

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Retrieves relationship-based interaction data for one or more RxNorm concepts (RxCUIs). You can pass a single RxCUI or a JSON array of RxCUIs and select a relationship type; the node returns a structured JSON object aggregating results per RxCUI.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/healthcare/rxnorm/saltairxnormdruginteractions.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node after obtaining RxCUIs (e.g., from a search or concept info node) when you want to explore potential interactions or relationships available via RxNorm for specific concepts. Typical workflow: search a drug to get RxCUIs, optionally refine selection, then query interactions/relationships using this node and inspect the returned JSON.

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
<tr><td style="word-wrap: break-word;">rxcui</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">RxNorm Concept Unique Identifier(s). Accepts either a single RxCUI string (e.g., "161") or a JSON array string of RxCUIs (e.g., "[\"161\", \"198211\"]").</td><td style="word-wrap: break-word;">["161", "198211"]</td></tr>
<tr><td style="word-wrap: break-word;">relationship_type</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Relationship to query for each RxCUI. Options include: boss_of, consists_of, constitutes, contained_in, contains, dose_form_of, doseformgroup_of, form_of, has_boss, has_dose_form, has_doseformgroup, has_form, has_ingredient, has_ingredients, has_part, has_precise_ingredient, has_quantified_form, has_tradename, ingredient_of, ingredients_of, inverse_isa, isa, part_of, precise_ingredient_of, quantified_form_of, reformulated_to, reformulation_of, tradename_of.</td><td style="word-wrap: break-word;">contained_in</td></tr>
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
<tr><td style="word-wrap: break-word;">drug_interactions</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">JSON string containing the queried RxCUIs, the relationship_type, and a data object mapping each RxCUI to its interaction/relationship results.</td><td style="word-wrap: break-word;">{"rxcui":["161"],"relationship_type":"contained_in","data":{"161":{...}}}</td></tr>
<tr><td style="word-wrap: break-word;">status</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Human-readable status message indicating success or describing any error encountered.</td><td style="word-wrap: break-word;">Successfully retrieved drug interactions for RXCUI ['161'] with relationship type contained_in</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Input format**: The rxcui input can be a single value (e.g., "161") or a JSON array string (e.g., "[\"161\", \"198211\"]"). If you pass a plain number or an improperly formatted string, parsing may fail.
- **Required input**: rxcui must not be empty; otherwise the node returns an error status.
- **Relationship scope**: The relationship_type determines which related data is returned. Not all relationship types may yield results for every RxCUI.
- **Output aggregation**: Results are aggregated in a map keyed by each RxCUI under the data field, wrapped with the requested relationship_type and input rxcui list.
- **API dependency**: Results depend on the availability and response of the underlying RxNorm API endpoints; transient issues may surface as API errors within the returned JSON.

## Troubleshooting
- **Empty RXCUI error**: If status shows "Error: RXCUI cannot be empty", provide a valid RxCUI or a JSON array of RxCUIs.
- **Invalid JSON array**: If you intend to pass multiple RxCUIs, ensure the rxcui input is a valid JSON array string (e.g., "[\"161\", \"198211\"]").
- **No results for a relationship**: If data is empty for certain RxCUIs, try a different relationship_type (e.g., has_ingredient or contains) or verify the RxCUIs are correct.
- **API Error in output**: If the returned JSON includes an API error message, retry later or validate that the relationship_type is supported for the given RxCUI.
- **Unexpected output format**: Confirm that relationship_type is one of the allowed options and that rxcui input is correctly formatted; malformed inputs can lead to partial or empty results.
