# DailyMed: NDC Lookup

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Looks up a U.S. National Drug Code (NDC) on DailyMed and returns structured product data, a human-readable summary, and a validation flag. It optionally validates NDC format before querying and can include packaging details in the summary.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/healthcare/dailymed/dailymedndclookupnode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you have an NDC and need authoritative product details (brand/generic names, manufacturer, active ingredients, dosage form, strength, and optional package info). Typical workflow: provide an NDC (with or without hyphens), enable format validation to catch bad inputs early, and connect the outputs to downstream nodes for display or further processing.

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
<tr><td style="word-wrap: break-word;">ndc_code</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The NDC to look up. Hyphens and spaces are allowed; they are normalized before the search.</td><td style="word-wrap: break-word;">0378-6015-10</td></tr>
<tr><td style="word-wrap: break-word;">validate_format</td><td>False</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">If true, checks that the NDC is 10 or 11 digits (after removing hyphens/spaces) before querying.</td><td style="word-wrap: break-word;">true</td></tr>
<tr><td style="word-wrap: break-word;">include_package_info</td><td>False</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">If true, includes a brief list of packaging details in the formatted summary.</td><td style="word-wrap: break-word;">true</td></tr>
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
<tr><td style="word-wrap: break-word;">product_info</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Raw product information as a JSON string from DailyMed for the first matching result or an error object if not found/invalid.</td><td style="word-wrap: break-word;">{"title": "Clonazepam...", "openfda": {"brand_name": ["Klonopin"]}, "product_ndc": ["0378-6015"], ...}</td></tr>
<tr><td style="word-wrap: break-word;">formatted_info</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">A human-readable summary of the product (product title, brand/generic names, manufacturer, active ingredients, strength, dosage form, and optional packaging lines).</td><td style="word-wrap: break-word;">NDC Product Information ============================== Product: Clonazepam ... Brand: Klonopin Generic: clonazepam Manufacturer: XYZ Pharma Active Ingredients: clonazepam Strength: 0.5 mg Dosage Form: tablet  Packaging Information:   - Bottle of 100 tablets (NDC: 0378-6015-10)</td></tr>
<tr><td style="word-wrap: break-word;">is_valid</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">True if the NDC format is acceptable and a product was found; false if format is invalid or no product was found.</td><td style="word-wrap: break-word;">true</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **NDC format**: Validation considers the NDC valid if it has 10 or 11 digits after removing hyphens/spaces. If validation is enabled and fails, the node returns an error and is_valid=false.
- **Single-result focus**: The node retrieves the first matching product for the provided NDC.
- **Packaging summary**: When enabled, only a limited number of package entries may be included in the formatted output for brevity.
- **Internet access required**: Successful lookups depend on external DailyMed availability and network connectivity.
- **Not found behavior**: If no product is found for a validly formatted NDC, the node returns an error message in product_info and formatted_info with is_valid=false.

## Troubleshooting
- **Invalid NDC format**: Ensure the NDC contains 10 or 11 digits after removing hyphens/spaces (e.g., 0378601510). Disable validate_format if you want to attempt a lookup anyway.
- **NDC not found**: Verify the code is correct, try removing hyphens, or check if the product is listed on DailyMed.
- **Empty or minimal fields**: Some products may lack certain fields (e.g., strength or packaging). This is expected based on source data.
- **Network or service issues**: If you receive a generic error, check your internet connection and try again later in case of DailyMed service interruptions.
