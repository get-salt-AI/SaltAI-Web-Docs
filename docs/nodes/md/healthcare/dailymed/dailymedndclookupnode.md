# DailyMed: NDC Lookup

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

This node queries the DailyMed service for a specific National Drug Code (NDC), optionally validates the NDC format, and returns both raw JSON product data and a human-readable summary. It integrates with the broader DailyMed API node to perform the actual search and handles not-found and error conditions gracefully. The node is tailored to quickly check a single NDC, including optional packaging information, and indicate whether the code is valid and resolved.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/healthcare/dailymed/dailymedndclookupnode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you have a specific NDC code and need authoritative drug information from the DailyMed dataset. Typical workflows include clinical decision support, medication reconciliation, pharmacy inventory tools, and any workflow where you need to confirm that an NDC corresponds to the expected product. Upstream, you might feed ndc_code from user input, OCR/scan of a medication label, or another parsing node that extracts NDCs from text. Downstream, the product_info JSON can be passed to analysis or summarization nodes (for example, an LLM-based clinical explainer), while formatted_info can be sent directly to UI/presentation nodes or notification integrations. The boolean is_valid is useful to gate subsequent logic branches (e.g., stop a prescribing flow if the code is invalid or not found). This node pairs naturally with DailyMedAPINode (for broader drug searches) and DailyMedIngredientSearchNode (to cross-check ingredients when only the NDC is known). For best practice, keep validate_format enabled in user-facing flows, and use include_package_info when packaging (such as unit dose vs. multi-dose) matters for your use case.

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
<tr><td style="word-wrap: break-word;">ndc_code</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The National Drug Code to look up. Hyphens and spaces are allowed and will be normalized. The node checks that the NDC is 10 or 11 digits in total once separators are removed. If validate_format is true and this check fails, the lookup is skipped and an error is returned.</td><td style="word-wrap: break-word;">0378-6015-10</td></tr>
<tr><td style="word-wrap: break-word;">validate_format</td><td>False</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">Whether to validate the basic structure of the NDC code before querying DailyMed. When true, the node strips hyphens/spaces and requires the remaining code to be all digits and either 10 or 11 digits long. If invalid, the node returns an error payload and is_valid set to false without calling the external API.</td><td style="word-wrap: break-word;">true</td></tr>
<tr><td style="word-wrap: break-word;">include_package_info</td><td>False</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">Controls whether detailed packaging information is included in the human-readable formatted_info output. When true, the formatted text will include package-level information where available (such as package size or configuration). When false, the summary focuses on product and clinical identifiers.</td><td style="word-wrap: break-word;">true</td></tr>
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
<tr><td style="word-wrap: break-word;">product_info</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">A JSON string containing the raw DailyMed/OpenFDA product record for the first result matching the NDC. On success, this typically includes fields such as title, openfda metadata (brand name, generic name, manufacturer, routes, etc.), and other structured drug attributes. On error (invalid format, not found, or runtime error), it contains an object with an error field describing the issue.</td><td style="word-wrap: break-word;">{ "title": "LORAZEPAM tablet", "openfda": { "brand_name": ["LORAZEPAM"], "generic_name": ["LORAZEPAM"], "manufacturer_name": ["MYLAN PHARMACEUTICALS INC."] }, "route": ["ORAL"], "dosage_form": "TABLET", "ndc": "0378-6015-10" }</td></tr>
<tr><td style="word-wrap: break-word;">formatted_info</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">A human-readable text summary of the product derived from the DailyMed/OpenFDA fields. This typically includes a header, product title, brand name, generic name, manufacturer, dosage form, route of administration, and optional packaging details. On failure, this will contain a short error message, such as why validation failed or that the NDC was not found.</td><td style="word-wrap: break-word;">NDC Product Information ============================== Product: LORAZEPAM tablet Brand: LORAZEPAM Generic: LORAZEPAM Manufacturer: MYLAN PHARMACEUTICALS INC. Dosage form: TABLET Route: ORAL Package: bottle of 100 tablets</td></tr>
<tr><td style="word-wrap: break-word;">is_valid</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">Indicates whether the NDC code passed validation and successfully resolved to a product. This will be false when the NDC format is invalid, when no product is found for a well-formed NDC, or when an internal error occurs during lookup. Use this flag to branch downstream logic or to trigger user-facing error handling.</td><td style="word-wrap: break-word;">true</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Performance**: The node performs a live lookup via the DailyMed API (indirectly through DailyMedAPINode), so latency depends on network conditions and the external service. Avoid calling it in tight loops over large NDC lists; instead, batch or throttle where possible.
- **Limitations**: Format validation only checks that the NDC is numeric and 10 or 11 digits long after removing separators; it does not verify that the code is assigned to a real product beyond the external API lookup.
- **Behavior**: If validate_format is enabled and the NDC fails the length/digit check, the node will not contact the external API and will immediately return an error payload with is_valid set to false.
- **Behavior**: When multiple products might conceptually map to an NDC, the node only returns the first result from the underlying DailyMed search. If your use case requires handling multiple matches, chain or extend the underlying DailyMedAPINode instead.
- **Limitations**: The structure of the product_info JSON depends on the DailyMed/OpenFDA schema and may change over time; downstream consumers should defensively handle missing or extra fields rather than relying on a fixed schema.

## Troubleshooting
- **Invalid NDC format**: If you see an error field with value "Invalid NDC format" in product_info and formatted_info says "Invalid NDC code format. Expected format: 5-4-2 or 4-4-2 digits", confirm that your NDC contains only digits (once hyphens/spaces are removed) and is 10 or 11 digits long, or disable validate_format for nonstandard codes.
- **NDC not found**: When product_info returns an error field with value "NDC not found" and is_valid is false, the code is syntactically valid but DailyMed has no record for it. Double-check that you are using the correct NDC (including any required labeler/product/package segments) and that the product is present in the DailyMed dataset.
- **Unexpected error during lookup**: If formatted_info starts with "Error: Error looking up NDC" and product_info contains an error field with more detail, there may be network issues, DailyMed downtime, or misconfiguration of the underlying DailyMedAPINode. Retry the request, verify network access to the service, and ensure any required environment or credentials for DailyMedAPINode are properly configured.
- **Empty or partial fields in formatted summary**: If formatted_info lacks brand, generic, or manufacturer lines, the underlying DailyMed/OpenFDA record may not include those fields. Inspect product_info JSON directly to confirm what data is available, and adjust any downstream parsing or UI expectations accordingly.
