# OpenFDA NDC Directory

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

This node queries the OpenFDA National Drug Code (NDC) Directory endpoint to retrieve structured drug product information. You select a drug attribute (such as brand name, generic name, dosage form, route, or active ingredient), provide a value, choose search or count, and optionally require exact matching. The node returns the OpenFDA `results` and `meta` sections as JSON strings for flexible downstream processing.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/healthcare/openfda/openfdandcnode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need authoritative FDA NDC Directory data to resolve or enrich drug product information. Typical situations include mapping brand names to active ingredients, inspecting dosage forms and routes, checking packaging descriptions, or computing counts of products by a given attribute. Place it after nodes that produce a drug-related query string (for example, from user input, database values, or NLP extraction) and before nodes that parse JSON, filter or transform results, or present information to users.

Example workflow: (1) A user or upstream node supplies a drug name like "aspirin". (2) Configure `field` to "brand name" or "generic name", set `field_value` to the supplied drug name, pick `action` = "search" to retrieve matching products, set `exact_match` to false for more tolerant matching, and choose a reasonable `limit` such as 25. (3) Pass the `results` output into a JSON parsing node to extract fields like `product_ndc`, `active_ingredients`, `dosage_form`, and `route`, then feed that into visualization or summarization nodes.

This node complements `OpenFDADProductLabelNode`, which focuses on drug label text rather than NDC product directory data. Use the NDC node when you care about product identification, ingredients, forms, routes, and packaging; use the label node when you need narrative sections like warnings or adverse reactions.

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
<tr><td style="word-wrap: break-word;">field_value</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Text value to search for in the chosen NDC field. It is inserted into the OpenFDA query as the value for the selected field. It may be a full or partial string unless `exact_match` is true. Do not include quotation marks; the node adds them when constructing the API URL.</td><td style="word-wrap: break-word;">aspirin</td></tr>
<tr><td style="word-wrap: break-word;">field</td><td>True</td><td style="word-wrap: break-word;">CHOICE</td><td style="word-wrap: break-word;">Which NDC Directory field to query. The node exposes user-friendly labels mapped to OpenFDA fields: "active ingredient name" (active_ingredients.name), "active ingredient strength" (active_ingredients.strength), "brand name" (brand_name), "generic name" (generic_name), "dosage form" (dosage_form), "route" (route), and "packaging description" (packaging.description). The selection determines which underlying OpenFDA field `field_value` is matched against.</td><td style="word-wrap: break-word;">brand name</td></tr>
<tr><td style="word-wrap: break-word;">action</td><td>True</td><td style="word-wrap: break-word;">CHOICE</td><td style="word-wrap: break-word;">Specifies whether to fetch full records or aggregated counts. Use "search" to retrieve detailed NDC records matching the query. Use "count" to get aggregated counts grouped by the selected field. Internally this becomes the `search` or `count` parameter in the OpenFDA request.</td><td style="word-wrap: break-word;">search</td></tr>
<tr><td style="word-wrap: break-word;">exact_match</td><td>True</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">If true, the node queries the `.exact` subfield (for example, brand_name.exact), requesting exact term matches from OpenFDA. This is useful when you need strict, normalized matching. If false, OpenFDA uses more flexible text search behavior, which is generally better for user-entered or noisy text.</td><td style="word-wrap: break-word;">false</td></tr>
<tr><td style="word-wrap: break-word;">limit</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Maximum number of items to return. For "search" actions this controls how many NDC records are returned; for "count" it limits the number of aggregation buckets. Must be at least 1. Larger values increase payload size and latency and may be constrained by OpenFDA.</td><td style="word-wrap: break-word;">25</td></tr>
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
<tr><td style="word-wrap: break-word;">results</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">JSON-stringified `results` array from the OpenFDA NDC endpoint. For "search", this is a list of product objects containing fields like `product_ndc`, `brand_name`, `generic_name`, `active_ingredients` (with `name` and `strength`), `dosage_form`, `route`, and `packaging`. For "count", it is typically a list of objects with `term` and `count` fields representing aggregated counts.</td><td style="word-wrap: break-word;">[{"product_ndc": "12345-6789", "brand_name": "ASPIRIN", "generic_name": "ASPIRIN", "dosage_form": "TABLET", "route": ["ORAL"], "active_ingredients": [{"name": "ASPIRIN", "strength": "325 MG"}], "packaging": [{"description": "Bottle of 100 tablets"}]}]</td></tr>
<tr><td style="word-wrap: break-word;">metadata</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">JSON-stringified `meta` object from the OpenFDA response. This usually includes the disclaimer, license URL, last update date, and a `results` sub-object with pagination details such as `skip`, `limit`, and `total`. Use this to understand how many records match your query and how the response was constrained.</td><td style="word-wrap: break-word;">{"disclaimer": "open.fda.gov disclaimer text", "license": "https://open.fda.gov/license/", "last_updated": "2024-01-15", "results": {"skip": 0, "limit": 25, "total": 134}}</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Performance**: Each run issues a live HTTPS request to the OpenFDA API with a 30-second timeout; high `limit` values or many rapid calls can increase latency and may approach external rate limits.
- **Limitations**: Data is limited to what OpenFDA publishes in the NDC Directory; some products or attributes (such as specific packaging details) may be missing, incomplete, or updated on a delay relative to other drug data sources.
- **Behavior**: When `exact_match` is true, the query uses the `.exact` variant of the field, which often returns fewer but more precise matches. Slight differences in spelling or formatting in `field_value` can lead to zero results.
- **Behavior**: If you choose or pass a field value that is not recognized in the internal mapping, it is sent directly as the field name to OpenFDA. Invalid or misspelled field names may cause errors or empty results.
- **Performance**: OpenFDA enforces its own rate and size limits. For workloads that perform many lookups, consider caching common queries or batching operations upstream to reduce repeated external calls.

## Troubleshooting
- **HTTP or API error**: If the node reports an HTTP error or fails to retrieve data, verify that OpenFDA is reachable, that `field` is set to one of the supported mapped labels, and that `limit` is within a modest range (for example, 1–100). Try simplifying the query or reducing `limit`.
- **Empty `results`**: If the `results` output is an empty array, check for typos in `field_value`, try turning off `exact_match`, or experiment with a different `field` (for example, `generic name` instead of `brand name`). Many user-entered terms do not exactly match stored FDA text.
- **Downstream JSON parse failures**: When nodes downstream fail with messages like "invalid JSON" or cannot access fields, confirm that they first parse the `results` and `metadata` strings as JSON. This node outputs strings, not pre-parsed objects.
- **Unexpected aggregate output**: If you receive only `term` and `count` objects instead of full NDC records, check that `action` is set to "search" rather than "count". Use "count" only when you explicitly want aggregated statistics.
- **Mismatch between `total` and returned rows**: If metadata shows a `total` larger than the length of `results`, this is expected: `total` is the total number of matching records on OpenFDA, while `limit` and pagination control how many were returned. Increase `limit` if you need more results, subject to API constraints.
