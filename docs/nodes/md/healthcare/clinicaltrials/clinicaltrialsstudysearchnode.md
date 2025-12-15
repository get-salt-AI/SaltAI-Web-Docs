# ClinicalTrials Study Search

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Queries the ClinicalTrials.gov v2 Studies endpoint and returns study search results along with metadata. Supports rich filtering (e.g., additional terms, location, sponsors, status) and sorting. Outputs either JSON results or raw CSV text depending on the selected format.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/healthcare/clinicaltrials/clinicaltrialsstudysearchnode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node to search for clinical studies by condition or disease and refine results using additional query and filter parameters. Typical workflow: provide a primary condition (e.g., "diabetes"), optionally select fields to return and additional filters (e.g., location, sponsor), choose the format (JSON for structured processing or CSV for tabular handling), and connect the results to downstream parsing or display nodes.

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
<tr><td style="word-wrap: break-word;">condition_or_disease</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Primary search query for conditions or diseases. Mapped to the ClinicalTrials API parameter query.cond.</td><td style="word-wrap: break-word;">diabetes</td></tr>
<tr><td style="word-wrap: break-word;">format</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Response format. JSON returns a studies array; CSV returns raw CSV text.</td><td style="word-wrap: break-word;">json</td></tr>
<tr><td style="word-wrap: break-word;">fields</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Optional comma-separated list of fields to include in the response.</td><td style="word-wrap: break-word;">NCTId,BriefTitle,Condition,OverallStatus</td></tr>
<tr><td style="word-wrap: break-word;">sort</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Sorting directive passed to the API (e.g., @relevance or field-based sorts).</td><td style="word-wrap: break-word;">@relevance</td></tr>
<tr><td style="word-wrap: break-word;">count_total</td><td>False</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">If true, requests the API to include the total count of matching studies.</td><td style="word-wrap: break-word;">true</td></tr>
<tr><td style="word-wrap: break-word;">other_terms</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Additional free-text search terms. Mapped to query.term.</td><td style="word-wrap: break-word;">type 2</td></tr>
<tr><td style="word-wrap: break-word;">location_terms</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Location-based search terms (country, city, etc.). Mapped to query.locn.</td><td style="word-wrap: break-word;">United States</td></tr>
<tr><td style="word-wrap: break-word;">title_acronym</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Search in titles or acronyms. Mapped to query.titles.</td><td style="word-wrap: break-word;">insulin</td></tr>
<tr><td style="word-wrap: break-word;">intervention_treatment</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Search by intervention/treatment terms. Mapped to query.incr.</td><td style="word-wrap: break-word;">metformin</td></tr>
<tr><td style="word-wrap: break-word;">outcome_measures</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Search by outcome measure terms. Mapped to query.outc.</td><td style="word-wrap: break-word;">HbA1c</td></tr>
<tr><td style="word-wrap: break-word;">sponsor_collaborator</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Search by sponsor/collaborator names. Mapped to query.spons.</td><td style="word-wrap: break-word;">NIH</td></tr>
<tr><td style="word-wrap: break-word;">lead_sponsor_name</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Search by lead sponsor name. Mapped to query.lead.</td><td style="word-wrap: break-word;">University of Example</td></tr>
<tr><td style="word-wrap: break-word;">study_ids</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Search by study IDs (comma-separated NCT IDs or other IDs). Mapped to query.id.</td><td style="word-wrap: break-word;">NCT01234567,NCT07654321</td></tr>
<tr><td style="word-wrap: break-word;">patient_search_area</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Patient-centric search area terms. Mapped to query.patient.</td><td style="word-wrap: break-word;">Boston, MA</td></tr>
<tr><td style="word-wrap: break-word;">filter_overallStatus</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Overall status filter as a comma-separated list. Mapped to filter.overallStatus.</td><td style="word-wrap: break-word;">RECRUITING,ENROLLING_BY_INVITATION</td></tr>
<tr><td style="word-wrap: break-word;">filter_ids</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Filter explicitly by IDs (comma-separated). Mapped to filter.ids.</td><td style="word-wrap: break-word;">NCT01234567,NCT07654321</td></tr>
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
<tr><td style="word-wrap: break-word;">results</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Search results. If format=json, a JSON string containing an array of studies. If format=csv, raw CSV text.</td><td style="word-wrap: break-word;">[{ "protocolSection": { ... } }, { ... }]</td></tr>
<tr><td style="word-wrap: break-word;">metadata</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Search metadata. For JSON: includes total_studies, query, api_version. For CSV: includes format, content_length, query.</td><td style="word-wrap: break-word;">{ "total_studies": 1234, "query": "diabetes", "api_version": "2.0" }</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Formats**: Supported formats are json and csv. JSON returns a studies array; CSV returns raw text.
- **Fields parameter**: Provide a comma-separated list of fields to limit payload size and select specific attributes.
- **Sorting**: Default sort is @relevance. Ensure the value conforms to ClinicalTrials.gov sorting rules.
- **Status filter**: The default filter_overallStatus is "AVAILABLE,COMPLETED". Adjust to include statuses like RECRUITING as needed.
- **Network behavior**: The request uses a 30-second timeout. Network or API errors yield an error JSON string in both outputs.
- **Empty results**: Strict filters can yield zero studies; relax filters (e.g., remove location or status limits) if needed.

## Troubleshooting
- **No results returned**: Broaden the search by removing or loosening filters like location_terms or filter_overallStatus.
- **Unexpected CSV content**: Verify the fields selection and that format is set to csv; CSV is returned as raw text.
- **Invalid sort value**: Use recognized sort directives (e.g., @relevance). Remove sort to fall back to the default.
- **Large payloads/slow responses**: Limit fields and avoid overly broad queries; consider using count_total sparingly.
- **Network error**: Check connectivity and try again. The node returns '{"error": "Network error"}' on failures.
- **JSON decode error**: Ensure format is set to json when expecting structured JSON; the node returns a JSON error string if parsing fails.
