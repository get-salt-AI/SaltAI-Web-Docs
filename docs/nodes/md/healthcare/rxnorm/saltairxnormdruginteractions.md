# RxNorm Drug Interactions

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Retrieves interaction-related data for one or more RxNorm concepts using the RxNorm API. You can provide a single RxCUI or a JSON array of RxCUIs, and select a relationship type to shape the returned interaction data. The node returns a JSON-formatted string of results and a status message.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/healthcare/rxnorm/saltairxnormdruginteractions.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need to analyze potential interaction relationships tied to RxNorm drug concepts. Commonly placed after a search or concept-lookup node that yields RxCUIs, it allows batch querying by passing a JSON array of RxCUIs to get consolidated results keyed by each concept.

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
<tr><td style="word-wrap: break-word;">rxcui</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">RxNorm Concept Unique Identifier. Accepts a single RxCUI string (e.g., "161") or a JSON array string of RxCUIs (e.g., "[161, 1049630]").</td><td style="word-wrap: break-word;">[161, 1049630]</td></tr>
<tr><td style="word-wrap: break-word;">relationship_type</td><td>True</td><td style="word-wrap: break-word;">CHOICE</td><td style="word-wrap: break-word;">Relationship type to use for interaction-related retrieval. Must be one of the provided options.</td><td style="word-wrap: break-word;">contained_in</td></tr>
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
<tr><td style="word-wrap: break-word;">drug_interactions</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">A JSON-formatted string containing interaction-related data for each input RxCUI, including the chosen relationship type and a per-RxCUI data object.</td><td style="word-wrap: break-word;">{   "rxcui": [161],   "relationship_type": "contained_in",   "data": {     "161": { "interactions": [/* ... */] }   } }</td></tr>
<tr><td style="word-wrap: break-word;">status</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Human-readable status message indicating success or describing any error encountered.</td><td style="word-wrap: break-word;">Successfully retrieved drug interactions for RXCUI [161] with relationship type contained_in</td></tr>
</tbody>
</table>
</div>

## Important Notes
- Input rxcui accepts either a single RxCUI string or a JSON array string; malformed JSON arrays will cause an error.
- The relationship_type must be one of: boss_of, consists_of, constitutes, contained_in, contains, dose_form_of, doseformgroup_of, form_of, has_boss, has_dose_form, has_doseformgroup, has_form, has_ingredient, has_ingredients, has_part, has_precise_ingredient, has_quantified_form, has_tradename, ingredient_of, ingredients_of, inverse_isa, isa, part_of, precise_ingredient_of, quantified_form_of, reformulated_to, reformulation_of, tradename_of.
- The node returns a JSON string; downstream nodes expecting structured objects should parse the string first.
- On errors (e.g., empty input, API issues), the node returns "{}" for drug_interactions and a status message describing the issue.
- Interaction data availability depends on the underlying API and the specific RxCUIs provided; some concepts may return no data.

## Troubleshooting
- Empty output {} with status mentioning RXCUI cannot be empty: Ensure the rxcui field is a non-empty string (e.g., "161") or a valid JSON array string (e.g., "[161]").
- Invalid JSON array string in rxcui: If providing multiple IDs, ensure the string is valid JSON (use double quotes only when needed and proper brackets).
- Unsupported relationship_type: Select from the provided dropdown list; arbitrary values are not accepted.
- API Error reported in status: Retry later or reduce batch size; network issues or upstream rate limits may be in effect.
- Partial results for multiple RxCUIs: Some RxCUIs may not have interaction-related data; check the per-RxCUI entries in the returned JSON under data.
