# DailyMed: Drug Search

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Queries the U.S. National Library of Medicine DailyMed API for drug information. Supports searching by NDC code, drug name, SPL set ID, or active ingredient. Returns raw JSON results, a human-readable summary, and the total result count.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/healthcare/dailymed/dailymedapinode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need authoritative drug label information, product details, active ingredients, dosage forms, and manufacturer metadata from DailyMed. Typical workflows include: validating a drug by NDC, exploring products by brand/generic name, retrieving a specific SPL by set ID, or compiling products that contain a given active ingredient.

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
<tr><td style="word-wrap: break-word;">search_type</td><td>True</td><td style="word-wrap: break-word;">CHOICE</td><td style="word-wrap: break-word;">The search mode. Supported options: ndc (product NDC), drug_name (brand/generic), set_id (SPL set ID), ingredient (active ingredient).</td><td style="word-wrap: break-word;">drug_name</td></tr>
<tr><td style="word-wrap: break-word;">query</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The query string to search for. Interpretation depends on search_type (e.g., an NDC like 0378-6015-10, a drug name like ibuprofen, an SPL set ID GUID, or an ingredient like acetaminophen).</td><td style="word-wrap: break-word;">ibuprofen</td></tr>
<tr><td style="word-wrap: break-word;">limit</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Maximum number of results to return. Applies to ndc, drug_name, and ingredient searches.</td><td style="word-wrap: break-word;">10</td></tr>
<tr><td style="word-wrap: break-word;">exact_match</td><td>False</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">If true, requests exact matching for drug_name and ingredient searches. Not applicable to ndc or set_id searches.</td><td style="word-wrap: break-word;">False</td></tr>
<tr><td style="word-wrap: break-word;">include_inactive</td><td>False</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">Intended to include inactive products. Note: This parameter is currently not used by the search and has no effect.</td><td style="word-wrap: break-word;">False</td></tr>
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
<tr><td style="word-wrap: break-word;">results_json</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Raw DailyMed results as a JSON string. For ndc, drug_name, and ingredient searches this is a JSON array; for set_id it is a single item wrapped into an array when present. If an error occurs, returns a JSON object with an "error" key.</td><td style="word-wrap: break-word;">[{"title":"IBUPROFEN","setid":"abcd-1234-...","openfda":{"brand_name":["IBUPROFEN"]}}]</td></tr>
<tr><td style="word-wrap: break-word;">formatted_results</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">A readable, multi-line summary of key fields (title, set ID, brand/generic names, manufacturer, active ingredients, NDCs, dosage form, and route). If no results, returns "No results found". If an error occurs, returns a message starting with "Error:".</td><td style="word-wrap: break-word;">DailyMed Search Results (Drug Name) ================================================== 1. Title: IBUPROFEN    Set ID: abcd-1234-...    Brand Name: IBUPROFEN    Generic Name: ibuprofen    Manufacturer: Example Pharma    Active Ingredients: Ibuprofen    NDC Codes: 12345-6789-01...</td></tr>
<tr><td style="word-wrap: break-word;">result_count</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">The number of results returned. Zero when no results or when an error is encountered.</td><td style="word-wrap: break-word;">5</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Search types**: ndc, drug_name, set_id, ingredient. Exact matching applies only to drug_name and ingredient.
- **NDC input**: Hyphens and spaces are stripped before querying. Provide a 10- or 11-digit NDC (e.g., 0378-6015-10).
- **Limits**: The limit parameter is honored for list endpoints and capped at 100.
- **Set ID retrieval**: The set_id search returns a single record (if found) and is wrapped in an array for output consistency.
- **Error handling**: On failure, results_json contains an object like {"error": "..."}, formatted_results begins with "Error:", and result_count is 0.
- **Network behavior**: Uses HTTPS calls to DailyMed services with a 30-second timeout per request.
- **Inactive products**: The include_inactive input is currently not used by the underlying requests and has no effect.

## Troubleshooting
- **No results returned**: Verify spelling and try without exact_match. For NDC, ensure the code is valid (10 or 11 digits after removing hyphens).
- **Error: Unsupported search type**: Use one of the supported values: ndc, drug_name, set_id, ingredient.
- **HTTP or timeout errors**: Check network connectivity, try again later, or reduce limit. DailyMed may be experiencing service issues or rate limiting.
- **Set ID not found**: Confirm the SPL set ID is correct (typically a GUID).
- **Unexpected output fields**: DailyMed responses can vary; some fields (e.g., strength, dosage_form, route) may be absent depending on the product.
