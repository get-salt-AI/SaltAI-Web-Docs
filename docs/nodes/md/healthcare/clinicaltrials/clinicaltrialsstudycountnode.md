# ClinicalTrials Study Count

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

This node calls the ClinicalTrials.gov v2 API to retrieve how many studies match a given condition or disease, returning both the raw API response and a concise metadata summary. It supports JSON, CSV, and XML output formats and enriches JSON responses with key fields such as totalStudies, minRank, maxRank, and apiVersion. It is built on the shared ClinicalTrials base node, which standardizes input options and query parameter construction.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/healthcare/clinicaltrials/clinicaltrialsstudycountnode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node whenever you need to quantify the volume of registered clinical research for a specific condition, disease, or search query. Typical use cases include: (1) building dashboards that show how many trials exist for a disease, (2) pre-filtering therapeutic areas before running more expensive analysis steps, and (3) comparing trial activity across conditions or time periods. In a typical workflow, you start with a text or parameter node that defines the condition_or_disease search query, optionally configure format and fields via ClinicalTrialsBaseNode-derived inputs, then feed those into ClinicalTrials Study Count. Downstream, send the results output to JSON or text analysis nodes (for JSON-format responses) or to file export/visualization nodes (for CSV/XML) and pass metadata to logging, audit, or monitoring nodes. Related nodes in the same family usually include other ClinicalTrials nodes that retrieve full study records or run more complex queries. Best practices: prefer JSON format when you plan further automated processing; only request specific fields via the base node’s input options when you want to minimize payload size; and handle error strings in downstream nodes by checking for an "error" key before parsing.

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
<tr><td style="word-wrap: break-word;">condition_or_disease</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Search query specifying the condition, disease, or other text criteria the ClinicalTrials.gov API should match against studies. This is passed into the ClinicalTrials.gov /studies endpoint via the base node’s query parameter builder. Use realistic medical terminology or free-text search phrases supported by ClinicalTrials.gov. Avoid excessively long or underspecified queries to keep results meaningful and performant.</td><td style="word-wrap: break-word;">metastatic non-small cell lung cancer</td></tr>
<tr><td style="word-wrap: break-word;">format</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Desired response format from the ClinicalTrials.gov API. Supported values are "json", "csv", and "xml". When "json" is selected, the node parses the response as JSON and returns a structured summary; for "csv" and "xml" it returns raw text content. Use lowercase values as expected by the API.</td><td style="word-wrap: break-word;">json</td></tr>
<tr><td style="word-wrap: break-word;">fields</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Optional comma-separated list of ClinicalTrials.gov field names to restrict which fields are included in the response. This is passed through to the API via the base node’s query parameter builder. Leave empty to use API defaults, or specify a focused subset to reduce payload size when you only need a few attributes. Field names must match ClinicalTrials.gov v2 API field identifiers.</td><td style="word-wrap: break-word;">NCTId,Condition,OverallStatus,StudyType</td></tr>
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
<tr><td style="word-wrap: break-word;">results</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The main response payload from ClinicalTrials.gov for the count query. When format=json, this is a JSON-formatted string containing: the raw API data under data plus derived keys like query, total_studies, min_rank, max_rank, and api_version. When format is csv or xml, this is the raw text returned by the API. Downstream nodes should treat it as a string and parse according to the chosen format.</td><td style="word-wrap: break-word;">{ "data": { "totalStudies": 842, "minRank": 1, "maxRank": 100, "apiVersion": "2.0", "studies": [ { "protocolSection": { "identificationModule": { "nctId": "NCT01234567" } } } ] }, "query": "metastatic non-small cell lung cancer", "total_studies": 842, "min_rank": 1, "max_rank": 100, "api_version": "2.0" }</td></tr>
<tr><td style="word-wrap: break-word;">metadata</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">A compact JSON-formatted string summarizing details about the query and response. For JSON format requests, it includes keys such as query, format, and api_version extracted from the API response. For CSV/XML formats, it includes format, content_length, and query. This is useful for logging, debugging, monitoring, or passing along lightweight context to downstream steps without the full payload.</td><td style="word-wrap: break-word;">{ "query": "metastatic non-small cell lung cancer", "format": "json", "api_version": "2.0" }</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Performance**: The node issues a live HTTP GET request to the ClinicalTrials.gov /studies endpoint with a 30-second timeout; complex or broad queries may approach this timeout and impact pipeline latency.
- **Limitations**: The node is constrained by ClinicalTrials.gov API availability, schema, and rate limits; if the external service is slow or down, this node will surface network error responses instead of data.
- **Behavior**: When format=json, the node parses the response and wraps it with additional summary fields; when format is csv or xml, it returns the raw text content and only minimal metadata, so downstream nodes must handle parsing accordingly.
- **Behavior**: On network errors, JSON decode errors, or unexpected exceptions, both outputs are set to small JSON strings with an error description instead of raising, so you must check for an error key before assuming success.

## Troubleshooting
- **Network error in ClinicalTrials.gov count query**: If both outputs contain '{"error": "Network error"}', the HTTP request likely failed (timeout, DNS, connectivity). Verify internet access from the environment, confirm ClinicalTrials.gov is reachable, and ensure any proxies or firewalls allow outbound HTTPS to the API.
- **JSON decode error in ClinicalTrials.gov count query**: If you see '{"error": "JSON decode error"}', the API response could not be parsed as JSON while format was set to json. Confirm that the format input is exactly json and that the ClinicalTrials.gov service is returning valid JSON for your query; if needed, temporarily switch to csv or xml to inspect the raw response.
- **Unknown error in ClinicalTrials.gov count query**: If both outputs are '{"error": "Unknown error"}', an unexpected condition occurred (such as an invalid combination of parameters). Simplify the query, remove or adjust the fields list, and re-run. Check any logging facilities wired to this node for more detailed error messages.
- **Empty or zero total_studies**: If the JSON summary shows total_studies: 0 despite expecting active research, refine the condition_or_disease query (for example, try broader terms, synonyms, or remove overly specific criteria) and ensure field names and filters supplied via the base node configuration are correct.
