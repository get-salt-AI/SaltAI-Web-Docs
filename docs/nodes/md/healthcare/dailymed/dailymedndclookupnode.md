# DailyMed: NDC Lookup

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Looks up a drug product by its NDC (National Drug Code) using the DailyMed public API. Optionally validates the NDC format before querying and returns both raw product data (as JSON) and a human-readable summary. If the NDC is invalid or not found, it returns an error message and marks the result as invalid.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/healthcare/dailymed/dailymedndclookupnode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need to retrieve authoritative U.S. drug product details by NDC, such as brand/generic names, manufacturer, active ingredients, dosage form, strength, and packaging. Typical workflow: provide an NDC (with or without hyphens), optionally enable format validation, and decide whether to include packaging details. Downstream nodes can consume the JSON output for structured processing, while the formatted summary is useful for display.

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
<tr><td style="word-wrap: break-word;">ndc_code</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The NDC to look up. Hyphenated (e.g., 0378-6015-10) or digits-only formats are accepted.</td><td style="word-wrap: break-word;">0378-6015-10</td></tr>
<tr><td style="word-wrap: break-word;">validate_format</td><td>False</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">If true, performs a basic format check (10 or 11 digits after removing hyphens/spaces) before querying DailyMed.</td><td style="word-wrap: break-word;">true</td></tr>
<tr><td style="word-wrap: break-word;">include_package_info</td><td>False</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">If true, includes a short list of packaging details in the formatted output.</td><td style="word-wrap: break-word;">true</td></tr>
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
<tr><td style="word-wrap: break-word;">product_info</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">JSON string containing the first matching product’s full DailyMed record or an error payload.</td><td style="word-wrap: break-word;">{"title": "Example Drug Label", "openfda": {"brand_name": ["BrandX"]}, "active_ingredient": ["acetaminophen"], ...}</td></tr>
<tr><td style="word-wrap: break-word;">formatted_info</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Human-readable summary of the product, including brand/generic names, manufacturer, active ingredients, dosage form, strength, and optional packaging.</td><td style="word-wrap: break-word;">NDC Product Information\n==============================\nProduct: Example Drug Label\nBrand: BrandX\nGeneric: acetaminophen\nManufacturer: Example Pharma Inc.\nActive Ingredients: acetaminophen\nStrength: 500 mg\nDosage Form: tablet\n\nPackaging Information:\n  - 100 tablets per bottle (NDC: 12345-6789-01)</td></tr>
<tr><td style="word-wrap: break-word;">is_valid</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">True when a product is found and (if enabled) the NDC format passes validation; false otherwise.</td><td style="word-wrap: break-word;">true</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Format validation is basic**: it only checks for 10 or 11 digits after removing hyphens/spaces; it does not enforce hyphenation patterns.
- **First match only**: the node returns the first product found for the provided NDC.
- **Packaging is truncated**: when included, only the first few packaging entries are summarized.
- **Network dependency**: requires internet access to reach the DailyMed API; network or API issues will result in error outputs.
- **Input flexibility**: both hyphenated and digits-only NDC codes are accepted.
- **Error handling**: if the NDC format is invalid or no product is found, the JSON output contains an error message, the formatted string explains the issue, and is_valid is false.

## Troubleshooting
- **Invalid NDC format message**: Ensure the NDC resolves to 10 or 11 digits after removing hyphens and spaces (e.g., 0378-6015-10 or 0378601510).
- **No product found**: Verify the NDC is correct and active; try removing hyphens or using the 11-digit version if available.
- **Network or API error**: Check internet connectivity and retry later; the formatted output will include an error description.
- **Missing fields in formatted output**: Some records may not include all fields (e.g., strength or manufacturer); this is normal for certain entries.
- **Unexpected multiple NDC variants**: If the NDC corresponds to multiple packages, only the first matching product is returned; consider searching by drug name if broader coverage is needed.
