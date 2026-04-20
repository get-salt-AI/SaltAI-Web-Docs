# ClinicalTrials Study Search

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

This node queries the ClinicalTrials.gov v2 /studies endpoint to retrieve studies matching a specified condition or disease, with optional advanced filters such as location, interventions, sponsors, and status. It can return structured JSON (as strings) or raw CSV/XML, along with metadata including total study counts and basic query details. It builds on a shared ClinicalTrials base node to standardize core inputs and query parameter handling across related ClinicalTrials nodes.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/healthcare/clinicaltrials/clinicaltrialsstudysearchnode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need to retrieve relevant clinical trials from ClinicalTrials.gov for a given condition, disease, or research question. Typical scenarios include tools for clinicians or patients to discover recruiting trials, evidence aggregation across a disease area, and feeding trial lists into downstream summarization, ranking, or analytics nodes. A common workflow is: an upstream node collects or infers a condition_or_disease string (for example, "Type 2 Diabetes"), optionally a set of fields and filters; ClinicalTrialsStudySearchNode then sends a query to ClinicalTrials.gov and returns a results string (studies) and a metadata string (summary info). Downstream, you might apply JSON parsing, filtering, and LLM-based summarization. Place this node near the beginning or in the middle of a pipeline whenever you need primary trial data. Start with simple queries and default filters to validate shape and content, then progressively add filters like location_terms, intervention_treatment, sponsor_collaborator, and filter_overallStatus to refine the result set. For quick counts without full details, pair this node with ClinicalTrialsStudyCountNode; for deep information on specific NCT IDs discovered here, chain into ClinicalTrialsStudyDetailNode. Prefer JSON format when the output will be parsed programmatically; use CSV if the next step expects tabular text.

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
<tr><td style="word-wrap: break-word;">condition_or_disease</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Primary search query string for the condition, disease, or set of key terms. It is sent as query.cond and is the main selector for which studies are returned. Keep it concise but descriptive.</td><td style="word-wrap: break-word;">Metastatic non-small cell lung cancer</td></tr>
<tr><td style="word-wrap: break-word;">format</td><td>True</td><td style="word-wrap: break-word;">STRING (choice: json\|csv)</td><td style="word-wrap: break-word;">Desired API response format. "json" returns a JSON payload, which this node converts to pretty-printed JSON strings; "csv" returns raw CSV text. Choose "json" for programmatic parsing; use "csv" for exports or systems expecting CSV.</td><td style="word-wrap: break-word;">json</td></tr>
<tr><td style="word-wrap: break-word;">fields</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Optional comma-separated list of ClinicalTrials.gov fields to include in the response. If empty, the API default field set is used. Limiting fields reduces payload size and can improve performance.</td><td style="word-wrap: break-word;">NCTId,BriefTitle,OverallStatus,Condition,InterventionName,LocationCity,LocationCountry</td></tr>
<tr><td style="word-wrap: break-word;">sort</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Sort order for returned studies, forwarded as the sort parameter. Default "@relevance" sorts by how well studies match the query. You may use other ClinicalTrials.gov-supported sort expressions (e.g., start date or status).</td><td style="word-wrap: break-word;">@relevance</td></tr>
<tr><td style="word-wrap: break-word;">count_total</td><td>False</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">If true, adds countTotal=true, requesting that the API return the total number of matching studies. This helps with pagination and understanding query breadth, at some cost to response time.</td><td style="word-wrap: break-word;">True</td></tr>
<tr><td style="word-wrap: break-word;">other_terms</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Additional free-text query terms, sent as query.term. Use this to refine results beyond condition_or_disease, such as adding biomarker, drug class, or outcome-related words.</td><td style="word-wrap: break-word;">PD-L1 inhibitor OR immunotherapy</td></tr>
<tr><td style="word-wrap: break-word;">location_terms</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Location-related search terms, sent as query.locn (e.g., countries, states, cities, or regions). Use to restrict trials geographically; multiple entries are typically comma-separated.</td><td style="word-wrap: break-word;">United States, Canada</td></tr>
<tr><td style="word-wrap: break-word;">title_acronym</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Title or acronym terms, sent as query.titles. Useful when you know partial trial names or acronyms and want to find those specific programs.</td><td style="word-wrap: break-word;">KEYNOTE OR CheckMate</td></tr>
<tr><td style="word-wrap: break-word;">intervention_treatment</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Intervention or treatment keywords, sent as query.incr. Use to find studies involving particular drugs, devices, procedures, or therapeutic classes.</td><td style="word-wrap: break-word;">pembrolizumab OR nivolumab</td></tr>
<tr><td style="word-wrap: break-word;">outcome_measures</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Outcome measure keywords, sent as query.outc, to focus on studies tracking specific endpoints. Helpful for filtering to clinically meaningful outcomes.</td><td style="word-wrap: break-word;">"overall survival" OR "progression-free survival"</td></tr>
<tr><td style="word-wrap: break-word;">sponsor_collaborator</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Sponsor or collaborator filter, sent as query.spons. Use to retrieve studies run or supported by particular organizations, institutions, or companies.</td><td style="word-wrap: break-word;">National Cancer Institute</td></tr>
<tr><td style="word-wrap: break-word;">lead_sponsor_name</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Lead sponsor filter, sent as query.lead. Restricts results to studies where a specific entity is listed as the lead sponsor.</td><td style="word-wrap: break-word;">Pfizer</td></tr>
<tr><td style="word-wrap: break-word;">study_ids</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Specific study identifiers, sent as query.id. Can include one or more NCT IDs or other supported IDs, often comma-separated. Useful for querying or reconciling a known trial set.</td><td style="word-wrap: break-word;">NCT04000165,NCT04567890</td></tr>
<tr><td style="word-wrap: break-word;">patient_search_area</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Patient-centric search area terms, sent as query.patient. Use to reflect how a patient might search for nearby or available trials, based on the API's supported semantics.</td><td style="word-wrap: break-word;">within 100 miles of Boston, Massachusetts</td></tr>
<tr><td style="word-wrap: break-word;">filter_overallStatus</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Comma-separated list of overallStatus values, sent as filter.overallStatus. Default "AVAILABLE,COMPLETED" focuses on currently available and completed trials. You can specify other values such as RECRUITING, ACTIVE, COMPLETED.</td><td style="word-wrap: break-word;">RECRUITING,ENROLLING_BY_INVITATION</td></tr>
<tr><td style="word-wrap: break-word;">filter_ids</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Identifier-based filter, sent as filter.ids. Use to constrain results to a specific set of study IDs directly in the API.</td><td style="word-wrap: break-word;">NCT04000165,NCT03999999,NCT04123456</td></tr>
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
<tr><td style="word-wrap: break-word;">results</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Study data returned by ClinicalTrials.gov. With format=json, this is a pretty-printed JSON string containing the "studies" array from the API response. With format=csv or xml, this is the raw CSV or XML text. Downstream nodes typically parse this string (for example, as JSON) before further filtering or summarization.</td><td style="word-wrap: break-word;">[   {     "protocolSection": {       "identificationModule": {         "nctId": "NCT04000165",         "briefTitle": "Pembrolizumab in Metastatic NSCLC"       },       "statusModule": {         "overallStatus": "RECRUITING"       }     }   },   {     "protocolSection": {       "identificationModule": {         "nctId": "NCT04567890",         "briefTitle": "Nivolumab Plus Chemotherapy in NSCLC"       },       "statusModule": {         "overallStatus": "ACTIVE_NOT_RECRUITING"       }     }   } ]</td></tr>
<tr><td style="word-wrap: break-word;">metadata</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Summary information about the response and query. For JSON responses, this typically includes total_studies (from totalStudies), query, and api_version. For CSV/XML, it includes format, content_length, and query. Always returned as a JSON-formatted string to simplify logging and inspection.</td><td style="word-wrap: break-word;">{   "total_studies": 128,   "query": "Metastatic non-small cell lung cancer",   "api_version": "2.0" }</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Performance**: Very broad queries for common conditions without filters can return large payloads and increase latency; prefer adding location, status, and field filters to keep results manageable.
- **Limitations**: The node exposes a subset of ClinicalTrials.gov v2 query parameters; complex boolean logic or unsupported filters may require post-processing of the returned studies rather than relying solely on the API query.
- **Behavior**: For JSON responses, the node extracts and returns only the "studies" array in results and moves values such as totalStudies and apiVersion into metadata, so the original top-level API response structure is not preserved.
- **Behavior**: On network, JSON decode, or unexpected errors, the node does not raise; it returns JSON strings like {"error": "Network error"} for both results and metadata, so downstream nodes should check for an "error" field before assuming valid data.

## Troubleshooting
- **Empty or unexpectedly small result set**: If results is empty or very small, loosen filters such as filter_overallStatus, location_terms, and intervention_treatment, and double-check the spelling and specificity of condition_or_disease.
- **Downstream JSON parsing failures**: If a JSON parser node fails on results, confirm that format was set to "json"; for "csv" responses, results is raw CSV text and cannot be parsed as JSON.
- **Network error in ClinicalTrials.gov API query**: When metadata contains {"error": "Network error"}, check that outbound HTTPS access to clinicaltrials.gov is allowed and stable, and consider retrying with a simpler query to rule out timeouts.
- **JSON decode error in ClinicalTrials.gov API query**: If metadata reports {"error": "JSON decode error"}, verify that format is "json" and that the service is returning valid JSON rather than an HTML error page or other content; transient service issues may require a later retry.
