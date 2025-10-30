# DailyMed: Drug Search

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Queries the National Library of Medicine’s DailyMed API to retrieve structured drug information. Supports searches by drug name, NDC, SPL set ID, and active ingredient, returning both raw JSON and a human-readable summary. Useful for quickly exploring labels, ingredients, dosage forms, routes, and manufacturer details.

</div>
<div style="flex: 0 0 300px;"><img src="../../../images/previews/New-Uncategorized/DailyMedAPINode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need authoritative drug label and product details from DailyMed. Choose a search_type (e.g., drug_name for general lookup, ndc for a specific product, set_id for a known SPL document, or ingredient to find products containing an active ingredient), provide a query, and set a result limit. Connect its outputs downstream to parsers, viewers, or decision logic that rely on either the raw JSON data or the formatted summary.

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
<tr><td style="word-wrap: break-word;">search_type</td><td>True</td><td style="word-wrap: break-word;">STRING (enum)</td><td style="word-wrap: break-word;">Determines the search mode: ndc, drug_name, set_id, or ingredient.</td><td style="word-wrap: break-word;">drug_name</td></tr>
<tr><td style="word-wrap: break-word;">query</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The search text corresponding to the chosen search_type. For ndc, hyphens/spaces are accepted and will be normalized.</td><td style="word-wrap: break-word;">aspirin</td></tr>
<tr><td style="word-wrap: break-word;">limit</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Maximum number of results to return for list searches (min 1, max 100). Not applicable to set_id (single-item).</td><td style="word-wrap: break-word;">10</td></tr>
<tr><td style="word-wrap: break-word;">exact_match</td><td>False</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">If true, requests exact matching for drug_name and ingredient searches.</td><td style="word-wrap: break-word;">False</td></tr>
<tr><td style="word-wrap: break-word;">include_inactive</td><td>False</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">Intended to include inactive products. Current implementation does not apply this filter to the API call.</td><td style="word-wrap: break-word;">False</td></tr>
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
<tr><td style="word-wrap: break-word;">results_json</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Raw JSON string of the DailyMed API response (array for list searches, single object for set_id).</td><td style="word-wrap: break-word;">[{ "title": "Aspirin 325 mg", "setid": "...", "openfda": {"brand_name": ["Aspirin"]}}]</td></tr>
<tr><td style="word-wrap: break-word;">formatted_results</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Human-readable summary of the results, including title, set ID, brand/generic names, manufacturer, active ingredients, NDCs, dosage form, and route.</td><td style="word-wrap: break-word;">DailyMed Search Results (Drug Name)\n==================================================\n1. Title: Aspirin 325 mg\n   Set ID: ...\n   Brand Name: Bayer\n   Generic Name: Aspirin\n   Manufacturer: Bayer HealthCare LLC\n   Active Ingredients: ASPIRIN\n   NDC Codes: 12345-6789-01...</td></tr>
<tr><td style="word-wrap: break-word;">result_count</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Number of returned results (0 when no matches or on error).</td><td style="word-wrap: break-word;">3</td></tr>
</tbody>
</table>
</div>

## Important Notes
- The node queries public DailyMed endpoints with a 30-second timeout per request.
- For search_type=ndc, hyphens/spaces are removed before querying.
- For search_type=set_id, only a single SPL document is returned; limit does not apply.
- The exact_match flag is passed to the API for drug_name and ingredient searches as an exact=true parameter.
- The include_inactive input is currently not applied to the API request and has no effect in this version.
- Result structures may vary by product; some fields (e.g., openfda, dosage_form, route, strength) may be absent.
- Large limits (near 100) can increase latency; consider smaller limits for interactive use.

## Troubleshooting
- No results found: Verify search_type and query spelling; try disabling exact_match or increasing limit.
- HTTP or timeout errors: Check network connectivity and try again; the API may be temporarily unavailable.
- Unexpected empty or missing fields: Different products expose different metadata; handle optional fields in downstream logic.
- Invalid search_type error: Ensure search_type is one of ndc, drug_name, set_id, or ingredient.
- NDC not matching: Try different NDC formats; the node normalizes hyphens/spaces but the code must exist in DailyMed.
