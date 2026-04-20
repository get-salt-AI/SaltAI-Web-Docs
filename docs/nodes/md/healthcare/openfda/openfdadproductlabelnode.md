# OpenFDA Drug Product Label

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

This node connects to the OpenFDA Drug Labels API (`/drug/label.json`) to fetch structured drug product label information such as warnings, adverse reactions, precautions, and other label sections. You specify which label field to search, the value to look for, and whether to perform a `search` or `count` operation, with an option to enforce exact matching. The node returns the raw `results` and `meta` portions of the OpenFDA response as JSON strings for downstream parsing and analysis.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/healthcare/openfda/openfdadproductlabelnode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when your Salt workflow requires authoritative FDA drug label text or sectioned label data, for example for clinical decision support, safety summaries, or compliance checking. Typical scenarios include building a drug information assistant that answers questions about side effects and warnings, generating structured summaries of label content for healthcare professionals or patients, or enriching internal databases with FDA label sections. Place this node early in the pipeline after you have a term or phrase to query—often from user input, an upstream database lookup, or another OpenFDA node such as `OpenFDANDCNode`.

A common workflow is: (1) collect a drug name like "ibuprofen" from a user form or prior node; (2) feed it into this node as `field_value`, with `field` set to a section like `warnings` or `adverse reactions`, `action` set to `search`, and `limit` set to a modest value; (3) send the resulting `results` JSON string to a downstream parsing node or LLM summarizer; and (4) display or store the extracted warnings or reactions. For analytics or monitoring, you can switch `action` to `count` to get aggregated counts over a given field. Best practices include keeping `limit` reasonable to avoid oversized responses, using `exact_match = false` for user-entered or exploratory queries, and enabling `exact_match` only when you need precise equality against specific label phrases or codes.

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
<tr><td style="word-wrap: break-word;">field_value</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The text value to search for in the selected label field. This can be a drug name, key phrase, or other relevant term and is inserted directly into the OpenFDA query expression. When `exact_match` is false, the API performs more flexible text matching; when `exact_match` is true, only records with an exact textual match in the chosen field are returned.</td><td style="word-wrap: break-word;">aspirin</td></tr>
<tr><td style="word-wrap: break-word;">field</td><td>True</td><td style="word-wrap: break-word;">CHOICE</td><td style="word-wrap: break-word;">The logical section of the product label to search. The node maps this human-readable choice to the corresponding OpenFDA field name. Mapped options include sections such as "abuse", "adverse reactions", "components", "drug interactions", "effective time", "general_precautions", "instructions for use", "nursing_mothers", "overdosage", "precautions", and "warnings".</td><td style="word-wrap: break-word;">warnings</td></tr>
<tr><td style="word-wrap: break-word;">action</td><td>True</td><td style="word-wrap: break-word;">CHOICE</td><td style="word-wrap: break-word;">Specifies which operation to perform on the OpenFDA endpoint. Use `search` to retrieve full label documents that match the query, which is appropriate for content retrieval and summarization. Use `count` to get aggregated counts by the specified field, which is useful for analytics, high-level monitoring, or quality checks.</td><td style="word-wrap: break-word;">search</td></tr>
<tr><td style="word-wrap: break-word;">exact_match</td><td>True</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">Controls whether to query the `.exact` variant of the chosen field. When set to true, the node appends `.exact` to the underlying OpenFDA field (for example `warnings.exact`), requiring an exact match of `field_value`. When false, the search is more flexible and can match partial or tokenized text as defined by the OpenFDA search behavior.</td><td style="word-wrap: break-word;">False</td></tr>
<tr><td style="word-wrap: break-word;">limit</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">The maximum number of results to request from the OpenFDA API. Must be at least 1. Higher values can provide more coverage but will increase response size and latency and may increase the chances of hitting OpenFDA rate or size limits.</td><td style="word-wrap: break-word;">10</td></tr>
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
<tr><td style="word-wrap: break-word;">results</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">JSON-encoded string containing the `results` array from the OpenFDA Drug Labels response. For `search` actions, this is an array of label documents, each potentially containing keys such as `warnings`, `adverse_reactions`, `overdosage`, `precautions`, `drug_interactions`, `components`, and other sections. For `count` actions, this contains aggregated count buckets for the requested field. Downstream nodes should parse this JSON string to access individual records and fields.</td><td style="word-wrap: break-word;">[{"id": "abcd1234", "warnings": ["Do not use in patients with known hypersensitivity to aspirin"], "adverse_reactions": ["Nausea", "Vomiting"], "effective_time": "20230101"}]</td></tr>
<tr><td style="word-wrap: break-word;">metadata</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">JSON-encoded string representing the `meta` object from the OpenFDA response. This typically includes properties such as `results.skip`, `results.limit`, and `results.total`, along with disclaimer and license information. It is useful for pagination, logging, and understanding how many records were available compared to how many were returned.</td><td style="word-wrap: break-word;">{"disclaimer": "openFDA is a beta research project...", "license": "https://open.fda.gov/license/", "results": {"skip": 0, "limit": 10, "total": 37}}</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Performance**: Each execution issues a live HTTPS GET request to the OpenFDA API with a 30-second timeout; large `limit` values or many concurrent calls can increase latency and may cause throttling.
- **Limitations**: The node can only access and return data present in the OpenFDA Drug Labels dataset; if certain labels or sections are missing or delayed in OpenFDA, they will not appear in the output.
- **Limitations**: OpenFDA enforces request and rate limits; workflows that generate heavy or rapid traffic may receive errors, partial responses, or slower performance from the external API.
- **Behavior**: The human-friendly `field` input is mapped internally to OpenFDA field names such as `abuse`, `adverse_reactions`, `components`, `drug_interactions`, `effective_time`, `general_precautions`, `instructions_for_use`, `nursing_mothers`, `overdosage`, `precautions`, and `warnings`.
- **Behavior**: When `exact_match` is true, the node queries the `.exact` version of the field, which typically narrows matches and may return zero results for loosely phrased or partial input values.
- **Behavior**: Both `results` and `metadata` outputs are raw JSON strings. Downstream consumers must parse these strings before working with nested fields or integrating them into further logic.

## Troubleshooting
- **HTTP 4xx/5xx errors or request exceptions**: These usually indicate an invalid query or external OpenFDA service issue. Confirm that `field` corresponds to a valid label section, `action` is `search` or `count`, and `limit` is a positive integer. If errors persist, slow down requests and check OpenFDA service status.
- **Empty or very small `results` array**: An empty or unexpectedly small `results` output typically means the query matched no records. Try disabling `exact_match`, choosing a more general `field` (for example `warnings` instead of a very specific section), or simplifying `field_value` to a broader term.
- **Timeout or connection failure**: Timeouts can occur if the OpenFDA API is slow or temporarily unavailable. Retry later, reduce `limit`, and avoid running many high-volume instances of this node in parallel.
- **JSON parsing errors downstream**: If subsequent nodes fail to parse `results` or `metadata`, inspect the raw strings to ensure they contain valid JSON and have not been truncated or concatenated. Make sure parsing happens exactly once and that consumers expect an array for `results` and an object for `metadata`.
- **Missing expected label sections**: If you do not see an expected section (such as `overdosage` or `nursing_mothers`) in the returned documents, check the full JSON structure of each record. Some labels do not include every section, and absence in OpenFDA is not necessarily a node error.
