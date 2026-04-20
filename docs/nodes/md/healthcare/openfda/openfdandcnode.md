# OpenFDA NDC Directory

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

This node queries the OpenFDA NDC Directory endpoint to retrieve structured information about drug products based on a selected field and value. It supports both search and count operations, with optional exact matching and configurable result limits. The node returns raw JSON results and metadata as strings for downstream analysis, display, or transformation.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/healthcare/openfda/openfdandcnode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need authoritative drug product information from the FDA’s NDC (National Drug Code) Directory, such as active ingredients, strengths, dosage forms, and packaging descriptions. It typically sits early in a workflow where you first query OpenFDA and then pass the results into parsing, analysis, or visualization nodes.

Common scenarios include: (1) searching by active ingredient name (for example, "aspirin") to retrieve all matching NDC records, (2) filtering by brand or generic name to build drug catalogs, (3) examining dosage form and route data for clinical or regulatory reporting, and (4) using count mode to understand how many products match a given criterion.

Typical upstream nodes: text or config providers like SaltStringNode (for field_value), SaltBooleanNode (for exact_match), and SaltIntegerNode (for limit). Typical downstream nodes: SaltDisplayAnyNode or SaltDisplayTableNode (to inspect results), SaltObjectNode or SaltListNode (to transform JSON into structures), LlmNode (to summarize drug information), or SaltChartNode (to visualize counts).

Best practices: Choose the most specific field in the "field" dropdown that matches your use case to reduce noise, and start with small "limit" values while testing. Use "exact_match" for precise filtering (for example, specific brand names) and switch "action" to "count" when you only need aggregate numbers rather than full records. Combine this node with validation or parsing nodes before feeding results into more complex analytics or language model steps.

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
<tr><td style="word-wrap: break-word;">field_value</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The value to search for in the selected NDC Directory field. This is a plain text string passed to the OpenFDA search or count query. Avoid including quotes; the node will construct the query correctly. There is no strict length limit from the node, but overly long strings may cause OpenFDA API errors or no matches.</td><td style="word-wrap: break-word;">ibuprofen</td></tr>
<tr><td style="word-wrap: break-word;">field</td><td>True</td><td style="word-wrap: break-word;">LIST</td><td style="word-wrap: break-word;">The logical field in the NDC Directory to search against. Internally, the node maps these human-readable labels to OpenFDA API field names. Available options include: "active ingredient name" (active_ingredients.name), "active ingredient strength" (active_ingredients.strength), "brand name" (brand_name), "generic name" (generic_name), "dosage form" (dosage_form), "route" (route), and "packaging description" (packaging.description). Choosing the correct field ensures accurate and efficient queries.</td><td style="word-wrap: break-word;">brand name</td></tr>
<tr><td style="word-wrap: break-word;">action</td><td>True</td><td style="word-wrap: break-word;">LIST</td><td style="word-wrap: break-word;">Defines whether to fetch records ("search") or just aggregated counts ("count"). "search" returns detailed NDC records matching the query. "count" returns count buckets for the specified field, which is useful for statistics and distributions. The default is "search".</td><td style="word-wrap: break-word;">search</td></tr>
<tr><td style="word-wrap: break-word;">exact_match</td><td>True</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">If true, the query uses the OpenFDA ".exact" variant of the selected field, requiring an exact text match. If false, partial or broader matches are allowed depending on the API behavior. Use exact matching for precise product or brand names; leave it false for broader discovery queries.</td><td style="word-wrap: break-word;">false</td></tr>
<tr><td style="word-wrap: break-word;">limit</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Maximum number of results to return from the OpenFDA API. Must be at least 1. Very large limits may be constrained by OpenFDA’s own caps or lead to slower responses. Start with small numbers (for example, 1–20) during experimentation and increase as needed.</td><td style="word-wrap: break-word;">10</td></tr>
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
<tr><td style="word-wrap: break-word;">results</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">JSON-encoded string containing the "results" array returned by the OpenFDA NDC Directory. For "search" actions, this is a list of product records with fields such as product_ndc, brand_name, generic_name, active_ingredients, dosage_form, route, and packaging. For "count" actions, this is typically an array of objects with "term" and "count" fields. Downstream nodes will usually parse this string into objects or tables.</td><td style="word-wrap: break-word;">[{"product_ndc": "59630-439-10", "brand_name": "IBUPROFEN", "generic_name": "IBUPROFEN", "active_ingredients": [{"name": "IBUPROFEN", "strength": "200 mg"}], "dosage_form": "TABLET", "route": "ORAL", "packaging": [{"description": "Bottle of 100 tablets"}]}]</td></tr>
<tr><td style="word-wrap: break-word;">metadata</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">JSON-encoded string of the "meta" object returned by OpenFDA, containing metadata such as disclaimer, license, last_updated, and results paging information. This is useful for debugging, logging, or showing query context, such as the total number of matching records and the time of data update.</td><td style="word-wrap: break-word;">{"disclaimer": "openFDA is a beta research project...", "license": "https://open.fda.gov/license/", "last_updated": "2024-01-15", "results": {"skip": 0, "limit": 10, "total": 245}}</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Performance**: Each execution performs a live HTTPS request to the OpenFDA API; response time depends on network latency and OpenFDA load. Keep "limit" modest and avoid overly frequent polling in automated workflows.
- **Limitations**: The node is constrained by OpenFDA’s rate limits and data coverage; some drugs or recent changes may not be present, and very large result sets may be truncated by API-side limits.
- **Behavior**: When "exact_match" is enabled, the node appends ".exact" to the underlying field name, which can significantly reduce results if the text does not match exactly as interpreted by OpenFDA.
- **Behavior**: If the OpenFDA response lacks a "results" or "meta" key, the node will still output valid JSON strings but with an empty list for results and/or an empty object for metadata.
- **Integration**: This node returns raw JSON as strings; downstream consumers must parse or interpret the JSON before doing structured operations like filtering, aggregation, or visualization.

## Troubleshooting
- **Common Error 1**: HTTP error or timeout when querying OpenFDA (for example, "HTTPError: 429 Client Error"). This typically indicates you have hit OpenFDA rate limits or there is a temporary outage. Reduce query frequency, lower "limit", and retry later. For robust workflows, add retry logic or backoff via orchestration.
- **Common Error 2**: No results returned (empty "results" array) even though you expect matches. Check that "field_value" is spelled correctly, that you selected an appropriate "field", and consider disabling "exact_match" for broader searches. Testing the query directly in the OpenFDA API explorer can help validate parameters.
- **Common Error 3**: Malformed or unexpected output when downstream nodes parse "results" or "metadata". Ensure downstream nodes treat these outputs as JSON strings and parse them before accessing properties. If parsing fails, log or display the raw outputs to inspect the exact structure returned by OpenFDA.
- **Common Error 4**: Workflow appears slow when many OpenFDA nodes are used in parallel. Consolidate similar queries where possible, reuse results, and avoid unnecessary high "limit" values to reduce network overhead and API load.
