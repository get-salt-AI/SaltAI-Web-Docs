# DailyMed: Ingredient Search

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Searches the U.S. DailyMed database for drug products that contain a specified active ingredient. Supports optional client-side filtering by dosage form, strength text, and manufacturer name. Returns both a JSON array of matching products and a human-readable summary with counts and breakdowns.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/healthcare/dailymed/dailymedingredientsearchnode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need to find all drug products containing a given active ingredient and optionally narrow results by dosage form, strength description, or manufacturer. Typical workflow: provide an ingredient (e.g., "acetaminophen"), set a result limit, optionally choose a dosage form (e.g., tablet) and text filters (e.g., "500 mg" for strength, a brand owner for manufacturer), then consume the JSON for downstream automation while displaying the summary to users.

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
<tr><td style="word-wrap: break-word;">limit</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Maximum number of products to retrieve from the API before local filters are applied. Range 1–100.</td><td style="word-wrap: break-word;">20</td></tr>
<tr><td style="word-wrap: break-word;">dosage_form</td><td>False</td><td style="word-wrap: break-word;">CHOICE</td><td style="word-wrap: break-word;">Optional dosage form filter applied after retrieval. Matching is case-insensitive and substring-based. Use 'any' to disable.</td><td style="word-wrap: break-word;">tablet</td></tr>
<tr><td style="word-wrap: break-word;">strength_filter</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Optional partial-text filter applied to the product's strength field. Case-insensitive substring match.</td><td style="word-wrap: break-word;">500 mg</td></tr>
<tr><td style="word-wrap: break-word;">manufacturer_filter</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Optional partial-text filter applied to the manufacturer name(s) reported by the product. Case-insensitive substring match.</td><td style="word-wrap: break-word;">Johnson</td></tr>
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
<tr><td style="word-wrap: break-word;">products_json</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">A JSON string array of product objects returned by DailyMed and filtered per the provided options.</td><td style="word-wrap: break-word;">[{"title":"Acetaminophen 500 mg","setid":"...","openfda":{"manufacturer_name":["Example Pharma"]},"active_ingredient":["ACETAMINOPHEN"],"dosage_form":["TABLET"],"strength":["500 mg"]}]</td></tr>
<tr><td style="word-wrap: break-word;">summary</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Human-readable summary including total count, most common dosage forms, top manufacturers, and strength variety.</td><td style="word-wrap: break-word;">Ingredient Search Summary: Acetaminophen ================================================== Total Products Found: 12  Dosage Forms:   - TABLET: 7 products   - CAPSULE: 3 products  Top Manufacturers:   - Example Pharma: 5 products  Strength Variations: 4 different strengths available</td></tr>
<tr><td style="word-wrap: break-word;">product_count</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Number of products after all local filters are applied.</td><td style="word-wrap: break-word;">12</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Data source**: Queries the DailyMed services API for U.S. drug product information.
- **Filter behavior**: dosage_form, strength_filter, and manufacturer_filter are case-insensitive substring matches applied after initial results are retrieved.
- **Missing fields**: Products lacking a dosage_form or strength will not match those respective filters.
- **Limit range**: limit accepts values from 1 to 100 and constrains the number of records fetched prior to local filtering.
- **Output on error**: On errors, products_json contains a JSON object with an "error" message, summary begins with "Error:", and product_count is 0.
- **Timeouts and availability**: Requests use a network timeout and depend on the external DailyMed API; transient network or service issues can affect results.
- **Geographic scope**: DailyMed focuses on U.S. prescription and OTC drug information.

## Troubleshooting
- **No products found**: Verify the ingredient spelling and broaden filters (set dosage_form to 'any', clear strength/manufacturer filters). Increase the limit to capture more candidates before filtering.
- **Too few results after filtering**: Your filters might be too strict or the data may not include those fields. Remove strength/manufacturer filters or set dosage_form to 'any'.
- **Unexpected exclusions with dosage_form**: Some records may not include dosage_form; selecting a specific form can exclude such entries. Use 'any' to include all.
- **Manufacturer filter not working**: Manufacturer names come from the product's openfda.manufacturer_name field. If absent, the product won't match; try removing this filter.
- **Network or API error**: If summary starts with "Error:" and products_json shows an error object, retry later, reduce limit, or check connectivity.
- **Rate limiting or service issues**: If receiving repeated errors or empty results unexpectedly, wait and retry to avoid temporary API throttling or outages.
