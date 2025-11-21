# DailyMed: NDC Lookup

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Looks up a specific NDC (National Drug Code) in the DailyMed database, optionally validating format beforehand. Returns the raw product record as JSON, a readable summary, and a boolean indicating whether the lookup succeeded.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/healthcare/dailymed/dailymedndclookupnode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you have an NDC and need to retrieve authoritative product details such as title, brand/generic names, manufacturer, active ingredients, dosage form, strength, and optional packaging info. It fits into workflows that validate user-entered NDCs, enrich medication data, or display concise drug summaries in apps or reports.

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
<tr><td style="word-wrap: break-word;">ndc_code</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The NDC to look up. Hyphens and spaces are allowed and will be normalized.</td><td style="word-wrap: break-word;">0378-6015-10</td></tr>
<tr><td style="word-wrap: break-word;">validate_format</td><td>False</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">If true, checks that the NDC is 10 or 11 digits after removing hyphens/spaces before querying.</td><td style="word-wrap: break-word;">true</td></tr>
<tr><td style="word-wrap: break-word;">include_package_info</td><td>False</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">If true, includes a brief list of package descriptions and package NDCs in the formatted output.</td><td style="word-wrap: break-word;">true</td></tr>
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
<tr><td style="word-wrap: break-word;">product_info</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Raw product data as a JSON string returned from the DailyMed service. Contains fields such as title, setid, openfda metadata, active_ingredient, dosage_form, route, strength, product_ndc, and packaging (when available).</td><td style="word-wrap: break-word;">{"title": "Example Drug", "setid": "xxxxx-xxxxx", "openfda": {"brand_name": ["Brand"], "manufacturer_name": ["Company"]}, "active_ingredient": ["ingredient"], "dosage_form": ["tablet"], "strength": ["500 mg"], "product_ndc": ["12345-6789"], "packaging": [{"description": "bottle of 100 tablets", "package_ndc": "12345-6789-10"}]}</td></tr>
<tr><td style="word-wrap: break-word;">formatted_info</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Human-readable summary of the product, including product title, brand/generic names, manufacturer, active ingredients, dosage form, strength, and (optionally) up to the first three packaging lines.</td><td style="word-wrap: break-word;">NDC Product Information\n==============================\nProduct: Example Drug\nBrand: Brand\nGeneric: Generic Name\nManufacturer: Company\nActive Ingredients: ingredient\nStrength: 500 mg\nDosage Form: tablet\n\nPackaging Information:\n  - bottle of 100 tablets (NDC: 12345-6789-10)</td></tr>
<tr><td style="word-wrap: break-word;">is_valid</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">Indicates whether the NDC passed format validation (if enabled) and a corresponding product was found.</td><td style="word-wrap: break-word;">true</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Format validation**: When enabled, the node treats NDCs as valid if they are 10 or 11 digits after removing hyphens/spaces. If invalid, no network request is made and is_valid will be false.
- **Normalization**: Hyphens and spaces in the NDC are removed before searching.
- **Not found behavior**: If no product matches the NDC, product_info contains an error JSON, formatted_info explains that nothing was found, and is_valid is false.
- **Packaging details**: When include_package_info is true, the formatted output lists up to the first three package entries, if any exist.
- **Network dependency**: This node requires network access to the DailyMed service; latency or outages may affect results.
- **Data variability**: Some fields (e.g., strength, dosage_form, route, openfda metadata) may be missing or be lists depending on the product record.

## Troubleshooting
- **Invalid NDC format reported**: Ensure the NDC is 10 or 11 digits when hyphens/spaces are removed (e.g., 5-4-2 or 4-4-2 patterns). You can disable validate_format to still attempt a lookup.
- **NDC not found**: Confirm the code is correct and active. Try removing hyphens or using an alternative code variant, or check if the product is present in the DailyMed database.
- **Network or timeout errors**: Retry later, verify internet connectivity, or reduce request frequency to avoid rate limiting.
- **Missing fields in output**: Some records may not include all attributes (e.g., strength or packaging). Check product_info JSON for available fields.
- **Unexpected formatted output**: Set include_package_info to false if you want a shorter summary, or inspect product_info to confirm the underlying data.
