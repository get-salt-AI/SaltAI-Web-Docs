# DailyMed: Drug Search

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Searches the U.S. DailyMed database for drug information by NDC, drug name, SPL set ID, or active ingredient. Returns both raw JSON and a human-readable summary with key fields like brand/generic name, manufacturer, dosage form, route, active ingredients, and NDC codes. Handles empty results and errors gracefully.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/healthcare/dailymed/dailymedapinode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need to look up drug labeling information or metadata from the DailyMed API. Typical workflows include validating a drug by NDC, exploring products by ingredient, fetching a specific SPL record by set ID, or listing products matching a drug name. Choose a search type, provide the query, and optionally enable exact matching for tighter results.

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
<tr><td style="word-wrap: break-word;">search_type</td><td>True</td><td style="word-wrap: break-word;">ENUM</td><td style="word-wrap: break-word;">The type of DailyMed search to run. Options: ndc, drug_name, set_id, ingredient.</td><td style="word-wrap: break-word;">drug_name</td></tr>
<tr><td style="word-wrap: break-word;">query</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The search query string. For ndc, accepts with or without hyphens; for set_id, provide the SPL Set ID; for drug_name or ingredient, provide the term to search.</td><td style="word-wrap: break-word;">aspirin</td></tr>
<tr><td style="word-wrap: break-word;">limit</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Maximum number of results to return (1–100). Ignored when search_type is set_id (returns a single record).</td><td style="word-wrap: break-word;">10</td></tr>
<tr><td style="word-wrap: break-word;">exact_match</td><td>False</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">If true, performs exact matching for drug_name and ingredient searches.</td><td style="word-wrap: break-word;">False</td></tr>
<tr><td style="word-wrap: break-word;">include_inactive</td><td>False</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">Optional flag for including inactive products. Note: This input is currently not applied to the API request.</td><td style="word-wrap: break-word;">False</td></tr>
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
<tr><td style="word-wrap: break-word;">results_json</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Raw JSON array (or object for set_id) of the matching DailyMed records returned by the API.</td><td style="word-wrap: break-word;">[{ "title": "ASPIRIN", "setid": "abc123...", "openfda": { "brand_name": ["Aspirin"], "generic_name": ["Aspirin"], "manufacturer_name": ["Example Pharma"] }, "active_ingredient": ["ASPIRIN"], "product_ndc": ["12345-6789"], "dosage_form": ["TABLET"], "route": ["ORAL"] }]</td></tr>
<tr><td style="word-wrap: break-word;">formatted_results</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Human-readable summary of results including title, set ID, brand/generic names, manufacturer, active ingredients, NDCs (up to three listed), dosage form, and route.</td><td style="word-wrap: break-word;">DailyMed Search Results (Drug Name) ==================================================  1. Title: ASPIRIN    Set ID: abc123...    Brand Name: Aspirin    Generic Name: Aspirin    Manufacturer: Example Pharma    Active Ingredients: ASPIRIN    NDC Codes: 12345-6789    Dosage Form: TABLET    Route: ORAL</td></tr>
<tr><td style="word-wrap: break-word;">result_count</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">The number of results returned. For set_id searches, this is 1 if found, otherwise 0.</td><td style="word-wrap: break-word;">5</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Search types and endpoints**: ndc, drug_name, and ingredient search the services/v2/spls.json endpoint; set_id fetches a single record via services/v2/spls/{set_id}.json.
- **Exact match behavior**: exact_match applies only to drug_name and ingredient searches; it is ignored for ndc and set_id.
- **NDC input**: Hyphens and spaces are removed before querying; both 10- and 11-digit NDCs are accepted.
- **Result formatting**: The formatted output lists up to the first three NDC codes per product for readability; the JSON contains full data.
- **Empty results**: If no matches are found, results_json is an empty array, formatted_results is "No results found", and result_count is 0.
- **Error handling**: Network or API errors return an error message in results_json and formatted_results, with result_count set to 0.
- **include_inactive**: Present as an input but not currently used in the request; it does not affect results.

## Troubleshooting
- **No results found**: Verify the query spelling, adjust search_type, increase limit, or disable exact_match to broaden results.
- **NDC not returning data**: Try removing/adding hyphens; ensure the NDC is valid and active in DailyMed.
- **Invalid set_id**: A nonexistent or malformed set ID will yield an error or zero results; confirm the correct SPL set ID.
- **Very few results with exact_match**: Set exact_match to false to include partial matches.
- **Network/API errors**: Check internet connectivity and retry later; the node uses a 30-second request timeout.
- **Large result sets**: If hitting the limit, paginate by running multiple queries with different terms or refine the query.
