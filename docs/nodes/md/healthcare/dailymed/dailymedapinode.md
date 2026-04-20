# DailyMed: Drug Search

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

This node integrates with the U.S. National Library of Medicine's DailyMed API to retrieve drug information by name, NDC code, SPL set ID, or active ingredient. It outputs the raw JSON data returned by DailyMed, a human-readable formatted summary of the results, and a count of how many records were found. It is intended for workflows that need authoritative label, NDC, ingredient, and manufacturer metadata.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/healthcare/dailymed/dailymedapinode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need to look up detailed, authoritative drug information inside a Salt workflow. Typical use cases include: resolving a text drug name (for example from a clinical note) into concrete labeled products and NDCs; enriching records or messages with official label details; supporting decision logic that depends on standardized drug metadata; and powering conversational or UI experiences that answer detailed medication questions. An upstream node usually provides the `query` string, such as a drug name, NDC, SPL set ID, or ingredient extracted from another system. You configure `search_type` to match that input and set a reasonable `limit` to control response size. The node then calls the appropriate DailyMed services endpoint and returns both `results_json` for machine consumption and `formatted_results` for direct display. Downstream, parse `results_json` with transformation or validation nodes, or store it; use `formatted_results` as a message body in chat, notification, or reporting nodes. For specialized tasks, pair this with `DailyMed: NDC Lookup` when you already have an NDC and want validation or packaging details, or `DailyMed: Ingredient Search` to explore products containing a given active ingredient. Because it depends on an external HTTP API, design workflows to tolerate network delays and occasional service unavailability, and keep `limit` modest for broad queries.

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
<tr><td style="word-wrap: break-word;">search_type</td><td>True</td><td style="word-wrap: break-word;">CHOICE(ndc\|drug_name\|set_id\|ingredient)</td><td style="word-wrap: break-word;">Chooses the DailyMed search mode. `ndc` searches by National Drug Code (hyphens and spaces are stripped). `drug_name` searches by product name. `set_id` retrieves a single Structured Product Label by its set ID. `ingredient` searches by active ingredient. This setting determines which API parameters and endpoints are used.</td><td style="word-wrap: break-word;">drug_name</td></tr>
<tr><td style="word-wrap: break-word;">query</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The term sent to DailyMed. For `drug_name`, a brand or generic name such as "ibuprofen". For `ndc`, an NDC string with or without hyphens; the node cleans formatting. For `set_id`, a valid DailyMed SPL set ID. For `ingredient`, the active ingredient name. Input is case-insensitive, but spelling and specificity affect which products are returned.</td><td style="word-wrap: break-word;">aspirin</td></tr>
<tr><td style="word-wrap: break-word;">limit</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Maximum number of records to return for list-style searches. Applies to `ndc`, `drug_name`, and `ingredient`. Must be between 1 and 100. Larger limits increase payload size and the length of the formatted summary, which can impact performance and readability.</td><td style="word-wrap: break-word;">10</td></tr>
<tr><td style="word-wrap: break-word;">exact_match</td><td>False</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">If true, requests an exact match for `drug_name` and `ingredient` searches by adding an `exact=true` parameter in the API call. Use this when you know the precise name and want to reduce partial or fuzzy matches. It has no effect when `search_type` is `ndc` or `set_id`.</td><td style="word-wrap: break-word;">False</td></tr>
<tr><td style="word-wrap: break-word;">include_inactive</td><td>False</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">Intended toggle for including inactive products. In the current implementation this flag is not passed through to the DailyMed API, so it does not change the results. Treat it as a no-op until support is added, and implement any active/inactive filtering in downstream logic if needed.</td><td style="word-wrap: break-word;">False</td></tr>
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
<tr><td style="word-wrap: break-word;">results_json</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">A JSON-encoded string of the raw DailyMed response data. For `drug_name`, `ndc`, and `ingredient` searches this is a JSON array of product objects from the API's `data` field. For `set_id`, it is a one-element array when the label exists or an empty array when not found. On failure it is a JSON object with an `error` field. Downstream nodes can parse this string to access fields like `title`, `setid`, `openfda` (brand_name, generic_name, manufacturer_name), `active_ingredient`, `product_ndc`, `dosage_form`, and `route`.</td><td style="word-wrap: break-word;">[{"title":"ASPIRIN 81 MG TABLET, DELAYED RELEASE","setid":"12345678-90ab-cdef-1234-567890abcdef","openfda":{"brand_name":["ASPIRIN LOW DOSE"],"generic_name":["ASPIRIN"],"manufacturer_name":["Example Pharma Inc"]},"active_ingredient":["ASPIRIN"],"product_ndc":["12345-6789"],"dosage_form":["TABLET, DELAYED RELEASE"],"route":["ORAL"]}]</td></tr>
<tr><td style="word-wrap: break-word;">formatted_results</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">A multi-line, human-readable summary of the search. It starts with a heading like "DailyMed Search Results (Drug Name)" and lists each product as a numbered entry. Each entry typically includes the product title, set ID, brand and generic names, manufacturer, active ingredients, a subset of NDC codes, dosage form, and route. This is suitable for direct presentation in chat, UI text areas, or logs without additional formatting.</td><td style="word-wrap: break-word;">DailyMed Search Results (Drug Name) ==================================================  1. Title: ASPIRIN 81 MG TABLET, DELAYED RELEASE    Set ID: 12345678-90ab-cdef-1234-567890abcdef    Brand Name: ASPIRIN LOW DOSE    Generic Name: ASPIRIN    Manufacturer: Example Pharma Inc    Active Ingredients: ASPIRIN    NDC Codes: 12345-6789    Dosage Form: TABLET, DELAYED RELEASE    Route: ORAL</td></tr>
<tr><td style="word-wrap: break-word;">result_count</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">The number of products returned and included in the formatted summary. For list-style searches it matches the length of the response `data` array (capped by `limit`). For `set_id` searches it is 1 when the label is found, or 0 if not found or if an error occurred.</td><td style="word-wrap: break-word;">1</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Performance**: Each execution sends one HTTPS request to DailyMed with a 30-second timeout. Using very high `limit` values or broad queries (for example common ingredients) can significantly increase response size and formatting time, so tune `limit` for your use case.
- **External dependency**: The node depends on the public DailyMed v2 services API. If that service is slow, down, or rate-limiting requests, the node will return an error payload instead of data. There is no built-in retry or backoff logic.
- **Data shape variability**: Some fields (such as `openfda`, `active_ingredient`, `product_ndc`, `dosage_form`, `route`) may be missing, empty, or represented as either strings or lists depending on the product. Downstream nodes must handle missing keys and multiple possible types.
- **Error handling behavior**: On exceptions (network errors, timeouts, unsupported `search_type`, or unexpected responses), the node logs the error, returns `results_json` as a JSON object with an `error` message, sets `formatted_results` to a short error description, and sets `result_count` to 0.
- **Exact match semantics**: The `exact_match` flag only applies to `drug_name` and `ingredient` searches and is implemented via an `exact=true` query parameter. The precise matching behavior is controlled by DailyMed and may still return close variants; add extra filtering in your pipeline if strict equality is required.
- **Inactive flag no-op**: The `include_inactive` parameter is not currently used when calling the API, so it has no practical effect. Do not rely on it to control whether inactive products are returned.

## Troubleshooting
- **No results found**: If `formatted_results` is "No results found" and `results_json` is `[]`, check spelling and specificity of the `query`, ensure `search_type` matches the value (for example do not use `drug_name` for an NDC), and consider turning off `exact_match` or increasing `limit` for broad queries.
- **HTTP or timeout error**: If `results_json` contains an object with a message like `"Error searching DailyMed: ..."` and `formatted_results` begins with `"Error:"`, there was a network, timeout, or service-side issue. Verify connectivity to `dailymed.nlm.nih.gov`, reduce `limit` to shrink responses, and consider adding retry logic around this node.
- **Downstream parsing failures**: If a downstream node fails due to missing fields (for example assuming `openfda.brand_name[0]` always exists), inspect `results_json` for a sample record and update parsing logic to handle missing keys and both scalar and list values gracefully.
- **Unsupported search_type**: If you see an error mentioning an unsupported search type, ensure `search_type` is exactly one of `ndc`, `drug_name`, `set_id`, or `ingredient`. Validate dynamically generated values before invoking this node.
- **Formatted output too long**: If `formatted_results` is too long for your UI or messaging channel, lower the `limit` or post-process the string to truncate or paginate results before displaying them to end users.
