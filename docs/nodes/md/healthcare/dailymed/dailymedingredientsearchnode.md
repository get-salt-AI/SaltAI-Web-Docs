# DailyMed: Ingredient Search

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Searches the DailyMed database for drug products that contain a specified active ingredient. Supports optional filtering by dosage form, strength (partial text match), and manufacturer (partial text match). Returns raw product data as JSON, a human-readable summary, and a count of matched products.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/healthcare/dailymed/dailymedingredientsearchnode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need to find drugs by active ingredient (e.g., 'acetaminophen') and optionally narrow results by dosage form, strength text, or manufacturer. Typical workflow: provide an ingredient and limit, optionally set filters, then pass the JSON results to downstream processing or use the summary for reporting.

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
<tr><td style="word-wrap: break-word;">ingredient</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Active ingredient name to search for in DailyMed.</td><td style="word-wrap: break-word;">acetaminophen</td></tr>
<tr><td style="word-wrap: break-word;">limit</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Maximum number of results to retrieve from DailyMed before applying local filters.</td><td style="word-wrap: break-word;">20</td></tr>
<tr><td style="word-wrap: break-word;">dosage_form</td><td>False</td><td style="word-wrap: break-word;">CHOICE</td><td style="word-wrap: break-word;">Filter results by dosage form. Use 'any' to disable this filter.</td><td style="word-wrap: break-word;">tablet</td></tr>
<tr><td style="word-wrap: break-word;">strength_filter</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Partial text filter applied to product strength values (case-insensitive). Leave empty to disable.</td><td style="word-wrap: break-word;">500 mg</td></tr>
<tr><td style="word-wrap: break-word;">manufacturer_filter</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Partial text filter applied to manufacturer names (case-insensitive). Leave empty to disable.</td><td style="word-wrap: break-word;">Johnson</td></tr>
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
<tr><td style="word-wrap: break-word;">products_json</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">JSON string array of matched DailyMed product objects after optional filtering.</td><td style="word-wrap: break-word;">[{...}, {...}]</td></tr>
<tr><td style="word-wrap: break-word;">summary</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Readable summary including total products found, top dosage forms, top manufacturers, and strength variety.</td><td style="word-wrap: break-word;">Ingredient Search Summary: Acetaminophen\n==================================================\nTotal Products Found: 18\n...</td></tr>
<tr><td style="word-wrap: break-word;">product_count</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Number of products returned after applying filters.</td><td style="word-wrap: break-word;">18</td></tr>
</tbody>
</table>
</div>

## Important Notes
- The search pulls up to 'limit' results from the API first, then applies local filters (dosage form, strength, manufacturer).
- Dosage form filter is a contains match against available dosage form text; use 'any' to disable.
- Strength and manufacturer filters are partial, case-insensitive matches; overly specific strings may yield zero results.
- Some products may lack fields like strength, dosage_form, or openfda.manufacturer_name; such items may be excluded by corresponding filters.
- products_json is a JSON string; parse it if you need structured access downstream.
- Network access is required; API timeouts or connectivity issues will return an error message in products_json and summary.

## Troubleshooting
- No results found: Broaden the ingredient spelling, increase limit, or remove/relax filters (set dosage_form to 'any', clear strength/manufacturer filters).
- Too few results: Increase 'limit' to fetch more candidates before filters are applied.
- Manufacturer filter not working: Ensure the product has openfda.manufacturer_name; items without it cannot match this filter.
- Unexpected empty strength matches: Strength values can vary in format; try shorter or more general substrings (e.g., '500' instead of '500 mg').
- API error or timeout: Check network connectivity and try again; consider reducing limit or retrying later if the service is rate-limiting.
