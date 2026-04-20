# ClinicalTrials Study Detail

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

This node queries ClinicalTrials.gov for a single study using its NCT identifier and returns the detailed study record plus response metadata. It supports JSON or CSV formats and can optionally limit the response to specific fields. The primary output is a text-encoded payload suitable for direct display, storage, or further parsing in downstream nodes.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/healthcare/clinicaltrials/clinicaltrialsstudydetailnode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need the full, authoritative details for a specific clinical trial identified by its NCT ID (for example, from a search results list or a known registry number). It typically appears downstream of search or lookup nodes that produce candidate NCT IDs, such as a study search node or an upstream query/UI that collects an NCT ID from a user. Connect its `results` output into analysis, summarization, or visualization nodes, and route `metadata` into logging, monitoring, or diagnostic nodes. Key use cases include pulling full protocol details for literature review or evidence synthesis, enriching a trial-centric knowledge base with structured registry data, generating patient- or clinician-facing summaries of a given trial, and validating or cross-checking trial information from other sources against the ClinicalTrials.gov record. A common pattern is: use a search or list node to identify relevant trials, then feed a selected `nct_id` into this node to fetch detailed information. Downstream, pair it with nodes that convert or display markdown, LLM summarization nodes, or data transformation nodes that parse JSON or CSV into structured tables. When building workflows, prefer `format = "json"` and `markupFormat = "markdown"` for most data-processing and human-readable outputs, and use the `fields` filter to minimize payload size and focus on the attributes your pipeline actually needs.

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
<tr><td style="word-wrap: break-word;">nct_id</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The ClinicalTrials.gov NCT identifier for the study you want to retrieve. It must be a valid NCT ID in the form 'NCT' followed by 8 digits (for example, NCT01234567). The ID should correspond to an existing trial in the ClinicalTrials.gov registry; otherwise, the request may return an empty result or an error depending on underlying service behavior.</td><td style="word-wrap: break-word;">NCT04000165</td></tr>
<tr><td style="word-wrap: break-word;">format</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The response format for the study detail payload. Supported options are 'json' and 'csv'. Use 'json' when you plan to parse or transform the data programmatically (recommended for most workflows). Use 'csv' when you need a tabular, spreadsheet-friendly output. The chosen format affects the structure of the text returned in `results` but not the kind of information retrieved.</td><td style="word-wrap: break-word;">json</td></tr>
<tr><td style="word-wrap: break-word;">markupFormat</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Controls how the study detail is prepared for textual markup. Options are 'markdown' for modern markdown-formatted output and 'legacy' for a simpler or backward-compatible layout. This mainly affects readability and formatting in downstream display or summarization nodes, not the underlying content. Choose 'markdown' for most user-facing or model-facing flows.</td><td style="word-wrap: break-word;">markdown</td></tr>
<tr><td style="word-wrap: break-word;">fields</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Optional list of specific fields to include in the response. When left empty, the node returns a comprehensive set of fields for the study. Use this to limit the payload to only the attributes you care about (for example, brief title, conditions, interventions, eligibility criteria). The exact field names depend on the ClinicalTrials.gov API schema used by the underlying service. Multiple fields are typically separated by commas or newlines.</td><td style="word-wrap: break-word;">NCTId,BriefTitle,OverallStatus,Condition,InterventionName,EligibilityCriteria</td></tr>
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
<tr><td style="word-wrap: break-word;">results</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The study details returned from ClinicalTrials.gov, encoded as a text string in the selected `format`. For `format = 'json'`, this is a JSON document (as text) containing the trial’s key attributes such as identifiers, titles, statuses, conditions, interventions, locations, eligibility, and outcome measures. For `format = 'csv'`, it is a CSV-formatted text string where fields correspond to trial attributes. This output is intended for analysis, parsing, storage, or display in downstream nodes.</td><td style="word-wrap: break-word;">{"StudyFieldsResponse":{"NCTId":["NCT04000165"],"BriefTitle":["A Study of XYZ Drug in Patients With Condition ABC"],"OverallStatus":["Recruiting"],"Condition":["Condition ABC"],"InterventionName":["XYZ Drug"],"EligibilityCriteria":["Inclusion Criteria:\n- Age 18 to 65 years..."],"StartDate":["2020-06-15"],"PrimaryCompletionDate":["2024-12-31"]}}</td></tr>
<tr><td style="word-wrap: break-word;">metadata</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Text-encoded metadata about the API call and response. This typically includes information such as the endpoint and parameters used, any requested fields, and response-level information like record counts and timestamps. Use this to debug, log, or audit your queries, or to manage workflows that need awareness of source and response context.</td><td style="word-wrap: break-word;">{"query":{"endpoint":"/studies","nct_id":"NCT04000165","format":"json","fields":"NCTId,BriefTitle,OverallStatus,Condition,InterventionName,EligibilityCriteria"},"responseInfo":{"recordsReturned":1,"totalRecords":1,"downloadTime":"2024-03-25T10:32:05Z"}}</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Performance**: The node issues a network call to the ClinicalTrials.gov-backed service for each execution, so latency depends on external API performance and network conditions; batching multiple NCT IDs is not supported in a single call by this node.
- **Limitations**: Only a single NCT ID can be queried per execution; to process many trials, you must loop or parallelize across multiple node instances, which may also increase load on the underlying service.
- **Behavior**: The `fields` filter can significantly change the shape and size of the returned payload; downstream nodes should not assume a fixed schema when `fields` is customized.
- **Behavior**: If an invalid or unknown `nct_id` is provided, the underlying service may return an empty result set or minimal data without throwing a hard error; workflows should check `results` content rather than assuming success.
- **Limitations**: The node depends on the schema and availability of the ClinicalTrials.gov API via the base clinical trials service; changes or outages in that upstream service can affect the data format or availability without changes to this node.

## Troubleshooting
- **Empty or minimal `results`**: If the `results` output is empty or missing key fields, verify that the `nct_id` is correct and actually exists in ClinicalTrials.gov. Also check whether you over-restricted the `fields` input so that important attributes were filtered out.
- **Unexpected format in `results`**: If a downstream parser fails, confirm that the `format` input matches what the downstream node expects, such as setting `format = 'json'` for JSON parser nodes and `format = 'csv'` for CSV-processing nodes.
- **Poorly formatted or hard-to-read text**: If study details look poorly formatted or inconsistent when displayed or summarized, switch `markupFormat` from `legacy` to `markdown` and ensure your downstream display or summarization nodes are configured to interpret markdown correctly.
- **Intermittent failures or timeouts**: When requests occasionally fail or take too long, this may be due to network or upstream API issues. Reduce concurrent calls, add retry logic or backoff in your workflow, and avoid unnecessary repeated requests for the same `nct_id` by caching or reusing prior results.
