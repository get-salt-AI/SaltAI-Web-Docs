# DailyMed: Ingredient Search

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

This node queries the DailyMed API for products containing a specified active ingredient and then applies optional client-side filters for dosage form, strength, and manufacturer. It returns the filtered products as a JSON string, a human-readable summary, and a product count, enabling ingredient-centric medication discovery and analysis workflows.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/healthcare/dailymed/dailymedingredientsearchnode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when your primary lookup key is an active ingredient, such as listing all products that contain acetaminophen, ibuprofen, or metformin. Place it after a step where the ingredient name is determined (for example, a user text input node, a diagnosis-to-ingredient mapping node, or a logic node that selects an ingredient based on prior context). Internally, the node calls the DailyMed API via the core DailyMed API node, retrieves up to `limit` matching products, and then narrows them further using the dosage form, strength, and manufacturer filters. Typical downstream consumers of `products_json` include JSON-processing or data transformation nodes that build tables, filter further by clinical rules, or select a specific product. The `summary` output is commonly passed to UI or text-generation nodes to provide readable explanations to clinicians or patients. This node pairs well with NDC-focused or detailed product-information nodes: you can first identify all relevant ingredient-based options here, then feed selected items onward for code-level integration or deeper metadata lookups. In practice, start with a moderate `limit` (around 20–50), pick an appropriate `dosage_form` to reduce noise, and refine further using `strength_filter` and `manufacturer_filter` when the result list is large or repetitive.

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
<tr><td style="word-wrap: break-word;">ingredient</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Active ingredient name to search for in DailyMed. This string is used to find products where the specified compound appears in the active ingredient list. Use precise substance names (e.g., 'acetaminophen', 'ibuprofen', 'metformin hydrochloride') rather than indications or brand names to obtain accurate and clinically meaningful results.</td><td style="word-wrap: break-word;">acetaminophen</td></tr>
<tr><td style="word-wrap: break-word;">limit</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Maximum number of products to fetch from DailyMed before local filters are applied. Must be an integer between 1 and 100. Larger values capture more possible matches but increase response size and processing time, especially when many products are later filtered out by dosage form, strength, or manufacturer.</td><td style="word-wrap: break-word;">20</td></tr>
<tr><td style="word-wrap: break-word;">dosage_form</td><td>False</td><td style="word-wrap: break-word;">CHOICE</td><td style="word-wrap: break-word;">Optional dosage-form filter applied after the ingredient-based search. Valid options are: 'any', 'tablet', 'capsule', 'liquid', 'injection', 'cream', and 'ointment'. When set to 'any', the node leaves dosage form unrestricted; otherwise, only products whose dosage form metadata matches the selected value are retained.</td><td style="word-wrap: break-word;">tablet</td></tr>
<tr><td style="word-wrap: break-word;">strength_filter</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Optional partial-text filter applied to the strength field of each product. If non-empty, only products whose strength text contains this value are kept. Use strength expressions that mirror labeling conventions, such as '500 mg' or '10 mg/mL'. Leave this empty to accept products of all strengths for the given ingredient.</td><td style="word-wrap: break-word;">500 mg</td></tr>
<tr><td style="word-wrap: break-word;">manufacturer_filter</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Optional partial-text filter for manufacturer name. When specified, the node includes only products whose manufacturer field contains this substring. This is useful to focus on products from preferred manufacturers, filter out certain brands, or ensure consistency in supply sources.</td><td style="word-wrap: break-word;">Generic Pharma Inc</td></tr>
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
<tr><td style="word-wrap: break-word;">products_json</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">JSON string representing the final list of products after applying the ingredient search and all optional filters. On success, this is typically a JSON array of product objects containing fields such as set_id, brand_name, generic_name, active_ingredient, strength, dosage_form, openfda.manufacturer_name, and packaging. On error, this field instead contains a JSON object with an 'error' key describing what went wrong.</td><td style="word-wrap: break-word;">[{"set_id":"1234abcd-5678-ef90-1234-56789abcdef0","brand_name":"Acetaminophen 500 mg","generic_name":["acetaminophen"],"active_ingredient":["ACETAMINOPHEN 500 mg"],"strength":["500 mg"],"dosage_form":["tablet"],"openfda":{"manufacturer_name":["Generic Pharma Inc"]},"packaging":[{"description":"Bottle of 100 tablets","package_ndc":"12345-6789-01"}]}]</td></tr>
<tr><td style="word-wrap: break-word;">summary</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Human-readable summary of the ingredient search results. This text typically includes the number of products found and, for each product, key attributes such as brand or generic name, active ingredients, strength, dosage form, manufacturer, and a concise subset of packaging details. It is formatted as multi-line text suitable for direct display or for use as context in narrative-generation components.</td><td style="word-wrap: break-word;">Found 8 products containing acetaminophen. Product: Acetaminophen 500 mg Generic Name: acetaminophen Manufacturer: Generic Pharma Inc Active Ingredients: ACETAMINOPHEN 500 mg Strength: 500 mg Dosage Form: tablet  Packaging Information:   - Bottle of 100 tablets (NDC: 12345-6789-01)</td></tr>
<tr><td style="word-wrap: break-word;">product_count</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Number of products remaining after all filters are applied. This corresponds to the length of the product list encoded in products_json when the search completes successfully, and gives a quick indication of how many matching options are available for the given ingredient and filter combination.</td><td style="word-wrap: break-word;">8</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Performance**: Higher `limit` values (near the maximum of 100) can substantially increase network latency and memory usage due to larger result sets; start with moderate limits and only increase them when you are confident you need more coverage.
- **Limitations**: Search results depend on DailyMed naming and data quality; misspellings, rare synonyms, or overly broad ingredient strings may yield incomplete or empty results, and not all real-world dosage forms map neatly to the fixed dosage_form choices provided by this node.
- **Behavior**: All optional filters—dosage_form, strength_filter, and manufacturer_filter—are applied after the initial DailyMed query, so stringent filters can reduce a large initial result set down to very few or zero products even if many products exist in the source data.
- **Behavior**: On exceptions such as connectivity problems or upstream service errors, the node logs the issue and returns products_json as a JSON object with an 'error' message and a summary string starting with error text; downstream components should detect and handle this case rather than assuming a normal product array.

## Troubleshooting
- **No products returned (product_count = 0)**: Verify that the ingredient name is spelled correctly and is a true active ingredient, then relax constraints by setting dosage_form to 'any', clearing strength_filter and manufacturer_filter, and optionally increasing limit to capture more candidates.
- **Downstream JSON parsing or iteration errors**: Check products_json; if an error occurred, it may be a JSON object with an 'error' field instead of an array of products. Adjust downstream logic to branch on the presence of an 'error' key before iterating or indexing.
- **Expected dosage form products not appearing**: If using a specific dosage_form excludes items you expect, switch dosage_form to 'any' and inspect the actual dosage_form values in products_json, as some products may use slightly different labels than the fixed options (for example, variants of oral solutions vs. generic 'liquid').
- **Too many similar generic products**: When the results include many near-duplicate generics, tighten the search by specifying a strength_filter (such as '500 mg') and/or a manufacturer_filter to narrow the list to the clinically relevant or preferred subset.
