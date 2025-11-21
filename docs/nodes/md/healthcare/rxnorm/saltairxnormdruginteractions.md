# RxNorm Drug Interactions

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Retrieves potential drug interaction information for one or more RxNorm concepts (RxCUIs) using the selected RxNorm relationship type. Accepts a single RxCUI or a JSON array string of multiple RxCUIs and returns a JSON-formatted summary keyed by each RxCUI.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/healthcare/rxnorm/saltairxnormdruginteractions.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node after you have identified one or more RxCUIs (e.g., via RxNorm search or by NDC lookup). Select the relationship type to explore interaction-related relationships, then feed the output JSON to downstream analysis or display. This is useful for quickly aggregating relationship-driven interaction data across multiple concepts.

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
<tr><td style="word-wrap: break-word;">rxcui</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">An RxNorm Concept Unique Identifier. Provide a single RxCUI (e.g., "161") or a JSON array string of RxCUIs (e.g., "[161, 198440]").</td><td style="word-wrap: break-word;">[161]</td></tr>
<tr><td style="word-wrap: break-word;">relationship_type</td><td>True</td><td style="word-wrap: break-word;">CHOICE</td><td style="word-wrap: break-word;">The RxNorm relationship type to use when retrieving interaction-related data. Options include: boss_of, consists_of, constitutes, contained_in, contains, dose_form_of, doseformgroup_of, form_of, has_boss, has_dose_form, has_doseformgroup, has_form, has_ingredient, has_ingredients, has_part, has_precise_ingredient, has_quantified_form, has_tradename, ingredient_of, ingredients_of, inverse_isa, isa, part_of, precise_ingredient_of, quantified_form_of, reformulated_to, reformulation_of, tradename_of.</td><td style="word-wrap: break-word;">contained_in</td></tr>
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
<tr><td style="word-wrap: break-word;">drug_interactions</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">A JSON string with the queried RxCUIs, selected relationship type, and data mapping each RxCUI to the retrieved results.</td><td style="word-wrap: break-word;">{   "rxcui": ["161"],   "relationship_type": "contained_in",   "data": {     "161": {"...": "..."}   } }</td></tr>
<tr><td style="word-wrap: break-word;">status</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">A human-readable status message indicating success or an error description.</td><td style="word-wrap: break-word;">Successfully retrieved drug interactions for RXCUI ['161'] with relationship type contained_in</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Input format**: If the rxcui string starts with '[' and ends with ']', it is parsed as a JSON array; otherwise it is treated as a single RxCUI.
- **Required field**: rxcui must be non-empty or the node returns an error.
- **Relationship types**: The provided relationship types are RxNorm relationships. Availability and meaning of results depend on RxNorm data for the selected type.
- **API dependency**: This node relies on an internal RxNorm API client; upstream API errors will be surfaced in the status and result JSON.
- **Output structure**: Results are returned as a JSON string. Downstream nodes expecting structured data must parse this string first.

## Troubleshooting
- **Empty RXCUI error**: Ensure the rxcui field is provided and not just whitespace.
- **Malformed array string**: When passing multiple RxCUIs, ensure the rxcui is a valid JSON array string (e.g., "["161", "198440"]" or "[161, 198440]").
- **Invalid relationship type**: Use one of the listed options. An invalid type may return empty results or an error from the API.
- **API errors**: Network or service issues will be returned in the status and embedded "error" fields. Retry later or reduce request volume.
- **Unexpected empty results**: Not all RxCUIs have data for every relationship type. Try a different relationship_type or validate the RxCUI.
