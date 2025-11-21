# DailyMed: Drug Search

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Searches the U.S. DailyMed database for drug information and returns both raw JSON and a human-readable summary. Supports multiple search modes (by drug name, NDC, set_id, or active ingredient), optional exact matching, and the ability to include inactive products.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/healthcare/dailymed/dailymedapinode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node to retrieve label and product details when you need authoritative drug information (e.g., during clinical data enrichment, medication verification, or building reference lookups). Choose the search type, provide a query, and set a result limit. The node outputs a JSON string for programmatic use and a formatted text summary for quick inspection.

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
<tr><td style="word-wrap: break-word;">search_type</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Select the search mode. Allowed values: ndc, drug_name, set_id, ingredient.</td><td style="word-wrap: break-word;">drug_name</td></tr>
<tr><td style="word-wrap: break-word;">query</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The search query string. Interpreted according to the selected search_type.</td><td style="word-wrap: break-word;">aspirin</td></tr>
<tr><td style="word-wrap: break-word;">limit</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Maximum number of results to return. Must be between 1 and 100.</td><td style="word-wrap: break-word;">10</td></tr>
<tr><td style="word-wrap: break-word;">exact_match</td><td>False</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">If true, restricts results to exact matches based on the search_type.</td><td style="word-wrap: break-word;">False</td></tr>
<tr><td style="word-wrap: break-word;">include_inactive</td><td>False</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">If true, includes inactive or discontinued products in search results.</td><td style="word-wrap: break-word;">False</td></tr>
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
<tr><td style="word-wrap: break-word;">results_json</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Raw DailyMed search results as a JSON-formatted string for programmatic parsing and downstream processing.</td><td style="word-wrap: break-word;">{"results": [{"brand_name": "Aspirin", "ndc": "0000-0000-00"}], "count": 1}</td></tr>
<tr><td style="word-wrap: break-word;">formatted_results</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Human-readable, line-broken summary of the matched products for quick review.</td><td style="word-wrap: break-word;">1) Aspirin — NDC: 0000-0000-00 — Active ingredient(s): Aspirin — Manufacturer: Example Pharma</td></tr>
<tr><td style="word-wrap: break-word;">result_count</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Number of results returned after applying filters and limits.</td><td style="word-wrap: break-word;">1</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Search modes**: 'ndc' expects an NDC code, 'drug_name' searches by brand/generic name, 'set_id' uses a DailyMed set identifier, and 'ingredient' searches by active ingredient.
- **Result limits**: The 'limit' input is constrained to 1–100 to balance performance with completeness.
- **Exact matching**: Enabling 'exact_match' can significantly narrow results; disable it to broaden discovery.
- **Inactive products**: Set 'include_inactive' to true if you need historical or discontinued product data.
- **Output parsing**: 'results_json' is a STRING containing JSON; parse it in downstream steps if you need structured fields.
- **API dependency**: Results depend on DailyMed’s public Services v2 endpoints; network conditions or upstream changes can affect output shape and availability.

## Troubleshooting
- **Zero results returned**: Try disabling 'exact_match', adjust the 'query' (e.g., remove dosage form), or switch 'search_type' (e.g., from drug_name to ingredient).
- **Unexpectedly few results**: Increase 'limit' and consider setting 'include_inactive' to true.
- **No matches for NDC**: Verify the NDC format and that the NDC exists in DailyMed; try without hyphens if the upstream service expects normalized values.
- **Slow or failed requests**: Check network connectivity and retry; large limits or transient DailyMed issues can cause timeouts.
- **JSON parsing errors downstream**: Ensure you parse the 'results_json' field as JSON; do not parse the formatted summary.
- **Inconsistent fields across items**: DailyMed entries vary by product; guard your downstream logic against missing or optional fields.
