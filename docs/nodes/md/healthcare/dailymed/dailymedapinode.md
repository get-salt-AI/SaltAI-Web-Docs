# DailyMed: Drug Search

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Searches the U.S. DailyMed database for drug information. Supports lookups by drug name, NDC code, SPL set ID, and active ingredient. Returns both raw JSON results and a human-readable summary with key fields like brand name, generic name, manufacturer, active ingredients, dosage form, route, and NDCs.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/healthcare/dailymed/dailymedapinode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node to retrieve authoritative drug labeling and product details for clinical, pharmacy, or safety workflows. Typical usage: select a search type (e.g., drug_name), provide a query (e.g., "aspirin"), and set a result limit. The node calls the DailyMed API and outputs JSON for downstream parsing plus a formatted summary for display or quick review.

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
<tr><td style="word-wrap: break-word;">search_type</td><td>True</td><td style="word-wrap: break-word;">CHOICE</td><td style="word-wrap: break-word;">Determines which DailyMed search endpoint to use. Supported values: ndc, drug_name, set_id, ingredient.</td><td style="word-wrap: break-word;">drug_name</td></tr>
<tr><td style="word-wrap: break-word;">query</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The search query string. Interpreted according to search_type. For ndc, hyphens/spaces are ignored; for set_id, use a DailyMed SPL set ID.</td><td style="word-wrap: break-word;">aspirin</td></tr>
<tr><td style="word-wrap: break-word;">limit</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Maximum number of results to return for list-based searches. Range 1–100. Ignored for set_id (returns one record).</td><td style="word-wrap: break-word;">10</td></tr>
<tr><td style="word-wrap: break-word;">exact_match</td><td>False</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">If true, requests exact matches for drug_name and ingredient searches. Has no effect for ndc or set_id.</td><td style="word-wrap: break-word;">False</td></tr>
<tr><td style="word-wrap: break-word;">include_inactive</td><td>False</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">Intended to include inactive products. Note: currently not applied to the request.</td><td style="word-wrap: break-word;">False</td></tr>
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
<tr><td style="word-wrap: break-word;">results_json</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">JSON string of the DailyMed API results. On error, returns a JSON string with an 'error' field.</td><td style="word-wrap: break-word;">[{"title": "ASPIRIN", "setid": "<uuid>", "openfda": {"brand_name": ["Aspirin"], "generic_name": ["Aspirin"], "manufacturer_name": ["Example Pharma"]}, "active_ingredient": ["ASPIRIN"], "product_ndc": ["12345-6789"], "dosage_form": "tablet", "route": ["oral"]}]</td></tr>
<tr><td style="word-wrap: break-word;">formatted_results</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Human-readable summary of the results with key fields per entry, suitable for display.</td><td style="word-wrap: break-word;">DailyMed Search Results (Drug Name)\n==================================================\n1. Title: ASPIRIN\n   Set ID: <uuid>\n   Brand Name: Aspirin\n   Generic Name: Aspirin\n   Manufacturer: Example Pharma\n   Active Ingredients: ASPIRIN\n   NDC Codes: 12345-6789\n   Dosage Form: tablet\n   Route: oral</td></tr>
<tr><td style="word-wrap: break-word;">result_count</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Number of results returned. 0 if none found or on error.</td><td style="word-wrap: break-word;">10</td></tr>
</tbody>
</table>
</div>

## Important Notes
- Exact matching only applies when search_type is 'drug_name' or 'ingredient'.
- For 'ndc' searches, hyphens and spaces in the input are removed before querying.
- For 'set_id', the node retrieves a single record (if found) and returns it as a single-item list.
- Network timeouts are set to approximately 30 seconds per request.
- If no results are found, the node returns an empty JSON array string, a 'No results found' message, and a count of 0.
- On error, outputs include a JSON string with an 'error' field, a formatted error message, and a count of 0.
- The 'include_inactive' input is present but not currently used to filter API results.

## Troubleshooting
- No results returned: Verify the query is appropriate for the selected search_type. For drug_name or ingredient, try disabling exact_match or increasing limit.
- NDC not found: Ensure the NDC is correct. Hyphens and spaces are normalized, but the code must be valid and present in DailyMed.
- Set ID lookup returns nothing: Confirm the SPL set ID is correct and active in DailyMed.
- HTTP or timeout errors: Check internet connectivity, firewall settings, and DailyMed service availability. Try again later or reduce request frequency.
- Unexpected fields missing in formatted output: DailyMed responses vary; some entries may not include all fields (e.g., openfda or strength). Parse results_json for robust handling.
- Invalid search_type error: Use one of 'ndc', 'drug_name', 'set_id', or 'ingredient'.
