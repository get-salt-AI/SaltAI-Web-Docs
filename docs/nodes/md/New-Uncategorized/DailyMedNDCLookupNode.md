# DailyMed: NDC Lookup

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Looks up drug product information by National Drug Code (NDC) using the DailyMed API. It can optionally validate the NDC format before querying and returns both raw product data (as JSON text) and a human-readable summary, along with a boolean indicating validity.

</div>
<div style="flex: 0 0 300px;"><img src="../../../images/previews/New-Uncategorized/DailyMedNDCLookupNode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you have an NDC and need detailed product information such as brand/generic names, manufacturer, active ingredients, strength, dosage form, and packaging. It fits well in workflows that validate user-entered NDCs, enrich medication records, or present a readable summary of a specific drug product.

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
<tr><td style="word-wrap: break-word;">ndc_code</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The NDC to look up. Hyphens and spaces are allowed; they will be normalized before the search.</td><td style="word-wrap: break-word;">0378-6015-10</td></tr>
<tr><td style="word-wrap: break-word;">validate_format</td><td>False</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">If true, checks the NDC format before searching. Accepts 10 or 11 digits (ignoring hyphens/spaces).</td><td style="word-wrap: break-word;">true</td></tr>
<tr><td style="word-wrap: break-word;">include_package_info</td><td>False</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">If true, includes packaging details (up to the first three package entries) in the formatted summary.</td><td style="word-wrap: break-word;">true</td></tr>
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
<tr><td style="word-wrap: break-word;">product_info</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Raw product information returned by the API, serialized as a JSON string. On error, contains a JSON error object.</td><td style="word-wrap: break-word;">{ "title": "Sample Drug", "openfda": { "brand_name": ["Brand"], "manufacturer_name": ["Company"] }, "product_ndc": ["12345-6789-01"] }</td></tr>
<tr><td style="word-wrap: break-word;">formatted_info</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">A readable summary of the product, including key fields like product title, brand/generic names, manufacturer, active ingredients, strength, dosage form, and optional packaging details.</td><td style="word-wrap: break-word;">NDC Product Information ============================== Product: Sample Drug Brand: Brand Manufacturer: Company Active Ingredients: Ingredient A Strength: 500 mg Dosage Form: tablet Packaging Information:   - Bottle of 100 (NDC: 12345-6789-01)</td></tr>
<tr><td style="word-wrap: break-word;">is_valid</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">Indicates whether the lookup is considered valid. False if the NDC format is invalid or no product is found; true if product data is returned.</td><td style="word-wrap: break-word;">true</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Format validation**: When enabled, only checks that the NDC contains 10 or 11 digits after removing hyphens/spaces; it does not guarantee the code exists.
- **Result scope**: Only the first matching product is returned for a given NDC; if multiple products exist, the node selects the first one.
- **Packaging details**: The formatted summary includes at most the first three package entries when packaging is present and include_package_info is true.
- **Network dependency**: Requires internet access to reach the DailyMed API; network or API issues will result in error outputs.
- **Data variability**: Not all products contain the same fields; some sections in the formatted summary may be omitted if data is missing.

## Troubleshooting
- **Invalid NDC format**: If you see an 'Invalid NDC format' error, ensure the NDC has 10 or 11 digits (hyphens/spaces are allowed but ignored).
- **NDC not found**: If 'NDC not found' is returned, verify the NDC is correct and active. Try removing hyphens or using the 11-digit version.
- **Timeouts or API errors**: Network issues can cause errors. Retry later, check connectivity, or reduce request frequency.
- **Incomplete formatted info**: If some fields are missing in the summary, the API may not provide those fields for the product.
- **Unexpected results**: If results do not match expectations, confirm the NDC corresponds to a product listed on DailyMed and consider checking variations (package NDC vs. product NDC).
