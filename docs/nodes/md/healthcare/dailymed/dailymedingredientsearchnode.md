# DailyMed: Ingredient Search

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Searches the U.S. DailyMed database for products containing a specified active ingredient. It retrieves up to a user-defined limit of results, then optionally filters them by dosage form, strength substring, and manufacturer substring. Returns both raw JSON and a human-readable summary that highlights dosage form distribution, top manufacturers, and strength variety.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/healthcare/dailymed/dailymedingredientsearchnode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need to find drug products by their active ingredient and quickly summarize what forms and manufacturers are most common. Typical workflow: set the ingredient and limit, optionally narrow by dosage form, and apply partial text filters for strength and manufacturer. Chain the products_json output to downstream processing or data extraction, and display the summary for quick insights.

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
<tr><td style="word-wrap: break-word;">ingredient</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The active ingredient name to search for. Matching is performed by the DailyMed service.</td><td style="word-wrap: break-word;">acetaminophen</td></tr>
<tr><td style="word-wrap: break-word;">limit</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Maximum number of results to fetch from the DailyMed service before optional local filters are applied. Range: 1–100.</td><td style="word-wrap: break-word;">20</td></tr>
<tr><td style="word-wrap: break-word;">dosage_form</td><td>False</td><td style="word-wrap: break-word;">CHOICE</td><td style="word-wrap: break-word;">Optional dosage form filter. Filters results locally by checking if the chosen form appears in the product's dosage_form text (case-insensitive).</td><td style="word-wrap: break-word;">tablet</td></tr>
<tr><td style="word-wrap: break-word;">strength_filter</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Optional partial-text filter applied to the product strength field (case-insensitive). Products without a strength field are excluded when this is set.</td><td style="word-wrap: break-word;">500 mg</td></tr>
<tr><td style="word-wrap: break-word;">manufacturer_filter</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Optional partial-text filter applied to manufacturer names (case-insensitive). Products without manufacturer info are excluded when this is set.</td><td style="word-wrap: break-word;">Johnson</td></tr>
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
<tr><td style="word-wrap: break-word;">products_json</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">JSON string array of the matched products returned from DailyMed after applying local filters.</td><td style="word-wrap: break-word;">[{"title":"ACETAMINOPHEN 500 MG","setid":"...","dosage_form":["TABLET"],"openfda":{"manufacturer_name":["Example Pharma"]}}]</td></tr>
<tr><td style="word-wrap: break-word;">summary</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Human-readable summary including total products found, top dosage forms, top manufacturers, and count of distinct strengths.</td><td style="word-wrap: break-word;">Ingredient Search Summary: Acetaminophen ================================================== Total Products Found: 18  Dosage Forms:   - TABLET: 10 products   - CAPSULE: 5 products  Top Manufacturers:   - Example Pharma: 7 products  Strength Variations: 6 different strengths available</td></tr>
<tr><td style="word-wrap: break-word;">product_count</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Number of products after applying all local filters.</td><td style="word-wrap: break-word;">18</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Filtering order**: The node first fetches up to 'limit' results from the service and only then applies local filters. If filters are strict, the final product_count may be much lower than 'limit'.
- **Partial matching**: strength_filter and manufacturer_filter are case-insensitive substring matches. dosage_form filtering is also case-insensitive and matches if the target form appears in the product's dosage_form text.
- **Missing fields**: Products missing dosage_form, strength, or manufacturer info will be excluded when the corresponding filter is set.
- **Dosage form choices**: Valid options are any, tablet, capsule, liquid, injection, cream, ointment. Choosing 'any' disables dosage form filtering.
- **Network dependency**: Requires network access to the DailyMed API; failures will return an error payload in products_json and a corresponding message in summary.
- **Result variability**: DailyMed data may vary by product; fields like strength, dosage_form, and manufacturer can be lists or strings.

## Troubleshooting
- **No results found**: Loosen or remove filters (especially strength_filter and manufacturer_filter) and/or increase 'limit'. Ensure the ingredient spelling is correct.
- **Very low product_count**: Remember filters apply after fetching. Increase 'limit' to retrieve more items before filtering, or relax the filters.
- **Network or API error**: Check connectivity and retry later. The outputs may contain an error JSON with an 'error' key and the summary will begin with 'Error:'.
- **Unexpected dosage form filtering**: The filter is substring-based and case-insensitive. Ensure the selected dosage_form matches how forms appear in the data (e.g., 'tablet' matches 'TABLET').
- **Missing manufacturer/strength**: Some products may not include these fields; when a filter is set, such products are excluded. Remove the specific filter to include them.
