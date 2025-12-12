# DailyMed: Ingredient Search

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Searches the DailyMed database for products containing a specified active ingredient. Supports optional filtering by dosage form, partial strength text, and partial manufacturer name. Returns raw JSON results, a human-readable summary, and the final product count after filters are applied.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/healthcare/dailymed/dailymedingredientsearchnode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need to find U.S. drug products by active ingredient and narrow the results to specific dosage forms, strengths, or manufacturers. Typical workflow: provide an ingredient (e.g., acetaminophen), optionally set filters (e.g., tablet, 500 mg, a manufacturer substring), then use the JSON output for downstream processing or the summary for display.

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
<tr><td style="word-wrap: break-word;">ingredient</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The active ingredient name to search for. Case-insensitive.</td><td style="word-wrap: break-word;">acetaminophen</td></tr>
<tr><td style="word-wrap: break-word;">limit</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Maximum number of results to fetch from the API before client-side filtering is applied.</td><td style="word-wrap: break-word;">20</td></tr>
<tr><td style="word-wrap: break-word;">dosage_form</td><td>False</td><td style="word-wrap: break-word;">ENUM</td><td style="word-wrap: break-word;">Optional dosage form filter. If set to 'any', no dosage-form filtering is applied. Matches are case-insensitive and substring-based.</td><td style="word-wrap: break-word;">tablet</td></tr>
<tr><td style="word-wrap: break-word;">strength_filter</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Optional partial-text filter for strength values. Case-insensitive substring match against strength fields.</td><td style="word-wrap: break-word;">500 mg</td></tr>
<tr><td style="word-wrap: break-word;">manufacturer_filter</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Optional partial-text filter for manufacturer names. Case-insensitive substring match.</td><td style="word-wrap: break-word;">johnson</td></tr>
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
<tr><td style="word-wrap: break-word;">products_json</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">JSON string array of matching product objects returned from the API, after applying filters.</td><td style="word-wrap: break-word;">[{"title": "Acetaminophen 500 mg", "openfda": {"manufacturer_name": ["Example Pharma"]}}]</td></tr>
<tr><td style="word-wrap: break-word;">summary</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Human-readable summary including total products, common dosage forms, top manufacturers, and strength variations.</td><td style="word-wrap: break-word;">Ingredient Search Summary: Acetaminophen ================================================== Total Products Found: 12  Dosage Forms:   - tablet: 8 products   - liquid: 3 products  Top Manufacturers:   - Example Pharma: 5 products  Strength Variations: 4 different strengths available</td></tr>
<tr><td style="word-wrap: break-word;">product_count</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">The number of products returned after applying optional filters.</td><td style="word-wrap: break-word;">12</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Filters are applied after retrieval**: The node fetches up to 'limit' results, then applies dosage form, strength, and manufacturer filters client-side.
- **Case-insensitive, partial matching**: dosage_form, strength_filter, and manufacturer_filter use substring, case-insensitive matching.
- **Dosage form options**: Supported values include 'any', 'tablet', 'capsule', 'liquid', 'injection', 'cream', and 'ointment'.
- **Result variability**: Not all products include all fields (e.g., strength or manufacturer), which may affect filter matches.
- **Limits**: The 'limit' input must be between 1 and 100.
- **No results behavior**: If no matches are found, products_json will be an empty array, summary will indicate no results, and product_count will be 0.
- **External API dependency**: Network issues or API downtime can cause empty results or error messages in the outputs.

## Troubleshooting
- **No products returned**: Increase 'limit' or loosen filters (set dosage_form to 'any', clear strength/manufacturer filters). Verify the ingredient spelling.
- **Unexpectedly low product_count**: Remember filters are substring, case-insensitive. Ensure your filter text actually appears in the data (e.g., try 'mg' instead of an exact '500mg').
- **Dosage form filter not working as expected**: The match is substring-based; ensure the selected option appears within the product's dosage form text.
- **Network/API errors**: Retry later or check connectivity. The products_json may contain an 'error' message if the API request failed.
- **Inconsistent manufacturer names**: Manufacturer data can be an array and vary in formatting. Try shorter substrings for broader matches.
