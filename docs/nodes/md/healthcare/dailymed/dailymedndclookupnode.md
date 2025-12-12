# DailyMed: NDC Lookup

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Looks up U.S. drug product information by National Drug Code (NDC) using the DailyMed dataset. Optionally validates the NDC format before querying and can include basic package details in a readable summary. Returns both raw JSON for downstream processing and a human-readable formatted summary, along with a boolean indicating validity/success.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/healthcare/dailymed/dailymedndclookupnode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you have an NDC and need authoritative product details such as brand/generic names, manufacturer, active ingredients, dosage form, and optional packaging info. Typical workflow: provide an NDC (with or without hyphens), enable format validation if you want a quick pre-check, and consume the JSON output for automation or the formatted text for display/logging.

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
<tr><td style="word-wrap: break-word;">ndc_code</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The NDC to look up. Hyphens and spaces are allowed (they will be normalized).</td><td style="word-wrap: break-word;">0378-6015-10</td></tr>
<tr><td style="word-wrap: break-word;">validate_format</td><td>False</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">If true, performs a simple format check (10 or 11 digits after removing separators) before querying.</td><td style="word-wrap: break-word;">true</td></tr>
<tr><td style="word-wrap: break-word;">include_package_info</td><td>False</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">If true, includes a brief packaging section (up to a few entries) in the formatted output.</td><td style="word-wrap: break-word;">true</td></tr>
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
<tr><td style="word-wrap: break-word;">product_info</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Raw product information as a JSON string from DailyMed for the first matching result.</td><td style="word-wrap: break-word;">{"title":"Example Drug","openfda":{"brand_name":["Brand"],"manufacturer_name":["Manufacturer"]}}</td></tr>
<tr><td style="word-wrap: break-word;">formatted_info</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Human-readable summary including product, brand/generic names, manufacturer, active ingredients, dosage form, and optional packaging details.</td><td style="word-wrap: break-word;">NDC Product Information\n==============================\nProduct: Example Drug\nBrand: Brand\nManufacturer: Manufacturer\nActive Ingredients: Ingredient A\nDosage Form: tablet\nPackaging Information:\n  - 100 tablets (NDC: 12345-6789-00)</td></tr>
<tr><td style="word-wrap: break-word;">is_valid</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">True when the lookup succeeded and the NDC passed validation (if enabled); false otherwise.</td><td style="word-wrap: break-word;">true</td></tr>
</tbody>
</table>
</div>

## Important Notes
- Validation is a basic length/digit check after removing hyphens/spaces (must be 10 or 11 digits). It does not guarantee the code exists.
- If validation fails, no API lookup is performed and outputs contain an error message with is_valid=false.
- When multiple products could match, only the first match is returned.
- Packaging details in the formatted output are truncated to a small number of entries for brevity.
- This node relies on the public DailyMed service; availability and response structure may change over time.

## Troubleshooting
- Invalid NDC format reported: Ensure the NDC has 10 or 11 digits after removing hyphens/spaces or disable validate_format to bypass the pre-check.
- NDC not found: Verify the NDC is correct, try variants with/without hyphens, or check if the product is listed in DailyMed.
- Empty or minimal formatted output: The source record may lack certain fields (e.g., strength, route, packaging). Try using the product_info JSON for complete raw data.
- Network or rate-limit errors: Retry after a short delay, check internet connectivity, or reduce request frequency.
- Unexpected fields or missing data: DailyMed records can vary by product. Use defensive parsing on the product_info JSON in downstream steps.
