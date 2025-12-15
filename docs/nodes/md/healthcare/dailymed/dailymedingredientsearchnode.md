# DailyMed: Ingredient Search

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Searches the DailyMed database for drug products that contain a specified active ingredient. Optionally filters the results by dosage form, strength text, and manufacturer name, then returns both raw JSON and a concise human-readable summary.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/healthcare/dailymed/dailymedingredientsearchnode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need to find U.S. drug products by active ingredient and narrow results with simple, case-insensitive filters. Typical workflow: provide an ingredient (e.g., acetaminophen), choose a result limit, optionally select a dosage form (e.g., tablet) and add partial text matches for strength (e.g., 500 mg) or manufacturer. Downstream nodes can consume the returned JSON for detailed processing, while the summary provides a quick overview of dosage forms, manufacturers, and strength variety.

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
<tr><td style="word-wrap: break-word;">ingredient</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Active ingredient name to search for in the DailyMed database. This is the core query.</td><td style="word-wrap: break-word;">acetaminophen</td></tr>
<tr><td style="word-wrap: break-word;">limit</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Maximum number of products to retrieve from DailyMed (1–100).</td><td style="word-wrap: break-word;">20</td></tr>
<tr><td style="word-wrap: break-word;">dosage_form</td><td>False</td><td style="word-wrap: break-word;">any \| tablet \| capsule \| liquid \| injection \| cream \| ointment</td><td style="word-wrap: break-word;">Optional filter to restrict results by dosage form. Uses a case-insensitive substring match against product dosage forms.</td><td style="word-wrap: break-word;">tablet</td></tr>
<tr><td style="word-wrap: break-word;">strength_filter</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Optional partial-text filter applied to the product strength field (case-insensitive).</td><td style="word-wrap: break-word;">500 mg</td></tr>
<tr><td style="word-wrap: break-word;">manufacturer_filter</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Optional partial-text filter applied to the manufacturer name(s) from the product’s OpenFDA metadata (case-insensitive).</td><td style="word-wrap: break-word;">Johnson</td></tr>
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
<tr><td style="word-wrap: break-word;">products_json</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">JSON string of the matching product records returned by the DailyMed API after local filters are applied.</td><td style="word-wrap: break-word;">[{ "title": "Acetaminophen 500 mg", "setid": "...", "active_ingredient": ["Acetaminophen"], "dosage_form": ["TABLET"], "strength": ["500 mg"], "openfda": { "manufacturer_name": ["Example Pharma"] }}]</td></tr>
<tr><td style="word-wrap: break-word;">summary</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Human-readable summary including total count, top dosage forms and manufacturers, and number of unique strengths.</td><td style="word-wrap: break-word;">Ingredient Search Summary: Acetaminophen ================================================== Total Products Found: 18  Dosage Forms:   - TABLET: 10 products   - CAPSULE: 5 products  Top Manufacturers:   - Example Pharma: 6 products  Strength Variations: 3 different strengths available</td></tr>
<tr><td style="word-wrap: break-word;">product_count</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">The number of products after applying all filters.</td><td style="word-wrap: break-word;">18</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Filters are case-insensitive and use partial substring matching** for dosage form, strength, and manufacturer.
- **Dosage form selection is optional**; choose "any" to disable this filter.
- **The API search itself is not exact-match**; ingredient searches use DailyMed's standard ingredient query without exact matching.
- **Some products may lack certain fields** (e.g., strength or manufacturer). Such items will not match those specific filters.
- **Network and API behavior**: Calls use a 30-second timeout and depend on DailyMed availability and rate limits.
- **Result limit range**: 1 to 100.

## Troubleshooting
- **No results returned**: Verify the ingredient spelling, increase the limit, and remove or relax filters (set dosage_form to "any", clear strength/manufacturer filters).
- **Too few results when filtering by dosage form**: Ensure the selected form is appropriate; try a broader form (e.g., tablet instead of caplet) or use "any".
- **Strength filter not matching**: The filter performs partial text match; try simpler text (e.g., "500" instead of "500 mg tablet").
- **Manufacturer filter not matching**: Some records may not include manufacturer data in OpenFDA fields; remove this filter or broaden the match.
- **API or network error**: Check connectivity and retry later. The node returns an error JSON in products_json and product_count = 0 on failure.
