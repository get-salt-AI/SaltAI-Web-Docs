# OpenFDA Drug Product Label

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

This node calls the OpenFDA /drug/label.json endpoint to retrieve information from FDA-approved drug product labels. You can search or count results across specific label sections such as warnings, adverse reactions, or precautions, with optional exact matching and a configurable result limit. The node returns the API results and metadata as JSON-formatted strings that downstream nodes can parse, analyze, or display.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/healthcare/openfda/openfdadproductlabelnode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need structured regulatory information directly from FDA drug product labels, such as safety warnings, adverse reaction profiles, or instructions for use. A common workflow is: (1) obtain or infer a drug keyword (for example from user input or another data source), (2) pass that term as field_value, (3) choose the appropriate label section via field (for example "warnings" or "adverse reactions"), (4) set action to either "search" to retrieve label documents or "count" to retrieve counts, (5) decide if you want exact_match for strict term matching, and (6) tune limit to control how many records you retrieve. This node often sits downstream of text or entity-extraction nodes that determine the drug name, and upstream of JSON parsing and summarization nodes that convert the JSON string into structured objects and human-readable summaries. It pairs well with OpenFDANDCNode: first use OpenFDANDCNode to identify relevant drug products in the NDC directory, then feed a selected name or identifier as field_value into OpenFDADProductLabelNode to fetch detailed labeling content. For robust workflows, start with narrow queries and small limits, inspect metadata to understand result volumes, and then expand if necessary.

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
<tr><td style="word-wrap: break-word;">field_value</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Term to search for within the selected label section. This string is inserted into the OpenFDA query as the value in field_name:"field_value". Use specific drug names, active ingredient names, or well-defined clinical terms. Very generic terms can lead to large or noisy results and slower responses.</td><td style="word-wrap: break-word;">ibuprofen</td></tr>
<tr><td style="word-wrap: break-word;">field</td><td>True</td><td style="word-wrap: break-word;">CHOICE</td><td style="word-wrap: break-word;">Logical label section to query, mapped internally to an OpenFDA field name. Supported values are: "abuse", "adverse reactions", "components", "drug interactions", "effective time", "general_precautions", "instructions for use", "nursing_mothers", "overdosage", "precautions", and "warnings". Picking the most relevant section ensures more focused and interpretable results.</td><td style="word-wrap: break-word;">warnings</td></tr>
<tr><td style="word-wrap: break-word;">action</td><td>True</td><td style="word-wrap: break-word;">CHOICE</td><td style="word-wrap: break-word;">Determines how the OpenFDA API is queried. Use "search" to retrieve matching label documents in the results array. Use "count" to retrieve aggregated counts grouped by the selected field instead of full documents. Choose "count" when you only need high-level statistics or want to gauge how many documents match before running a full search.</td><td style="word-wrap: break-word;">search</td></tr>
<tr><td style="word-wrap: break-word;">exact_match</td><td>True</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">If true, the query uses the .exact variant of the selected field (for example, warnings.exact), enforcing exact string matches on field_value. This is useful for precise terms like a specific brand or generic name. If false, OpenFDA applies tokenized search, which can return broader, partial, or fuzzy matches.</td><td style="word-wrap: break-word;">true</td></tr>
<tr><td style="word-wrap: break-word;">limit</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Maximum number of records to return. Must be at least 1. Larger limits return more labels but increase response size and latency. Adjust this based on how much data you intend to analyze downstream and any performance constraints in your workflow.</td><td style="word-wrap: break-word;">10</td></tr>
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
<tr><td style="word-wrap: break-word;">results</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">JSON-formatted string containing the results array from the OpenFDA Drug Label API. Each array element represents a single drug label document and may contain fields like id, effective_time, and multiple text sections (for example, warnings, adverse_reactions, drug_interactions). Downstream nodes typically parse this string into structured data for filtering, transformation, or summarization.</td><td style="word-wrap: break-word;">[{"id": "1234567","effective_time": "20240115","warnings": ["Do not use in patients with known hypersensitivity to ibuprofen."],"adverse_reactions": ["Nausea, dizziness, and headache were the most common adverse reactions observed in clinical trials."]}]</td></tr>
<tr><td style="word-wrap: break-word;">metadata</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">JSON-formatted string containing the meta object from the OpenFDA response. This includes disclaimer, terms, license, last_updated, and a results sub-object with skip, limit, and total counts. Use this to understand the size of the matching set, verify paging parameters, and track data currency.</td><td style="word-wrap: break-word;">{"disclaimer": "Do not rely on openFDA to make decisions regarding medical care.","terms": "https://open.fda.gov/terms/","license": "https://open.fda.gov/license/","last_updated": "2024-01-20","results": {"skip": 0,"limit": 10,"total": 275}}</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Performance**: Broad queries (common terms, no exact_match) combined with high limit values can be slow and may stress rate limits on the OpenFDA API; start with small limits and refine your query before scaling up.
- **Limitations**: The node only exposes a fixed set of label sections via its field_map; querying arbitrary or newly added OpenFDA fields is not possible without changes to the underlying node configuration.
- **Behavior**: When exact_match is enabled, the query uses the .exact version of the field, which often returns fewer but more precisely matched documents compared to non-exact searches.
- **Behavior**: Both outputs are raw JSON strings; they are not automatically deserialized. Any downstream processing that needs structured access to label fields must include an explicit JSON parsing step.

## Troubleshooting
- **Empty or unexpectedly small results set**: Check whether exact_match is set to true. If so, try disabling it or simplifying field_value. Also confirm that field is the most appropriate section; relevant information may be in warnings rather than precautions, or vice versa.
- **Errors about invalid field or query**: Ensure that field is chosen from the provided options and that action is either "search" or "count". If errors persist, simplify field_value to remove problematic characters and retry.
- **Slow responses or timeouts**: Reduce limit, make field_value more specific, or temporarily switch to action = "count" to inspect how many documents match before running a full search. Very high totals with broad queries can significantly slow down your workflow.
- **Downstream node cannot parse outputs**: Verify that the downstream node expects a string and that it is configured to parse JSON. If not, insert a JSON parsing node between OpenFDADProductLabelNode and the consumer to convert results and metadata into structured objects.
