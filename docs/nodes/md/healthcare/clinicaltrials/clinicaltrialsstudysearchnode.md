# ClinicalTrials Study Search

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Queries the ClinicalTrials.gov v2 /studies endpoint to find clinical studies matching a condition or disease and optional filters. Returns either a JSON-formatted list of studies with summary metadata or raw CSV content depending on the selected format. Supports fine-grained query parameters such as location, interventions, sponsors, status filters, and sorting.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/healthcare/clinicaltrials/clinicaltrialsstudysearchnode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node to search for clinical trials by condition (e.g., "lung cancer") and refine results with additional terms, location filters, sponsors, and study status. Choose JSON when you need a structured list of studies for downstream processing, or CSV for spreadsheet-like output. Typically used early in a workflow to collect study candidates before fetching details or performing analytics.

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
<tr><td style="word-wrap: break-word;">condition_or_disease</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Primary search term for the condition or disease. Sets query.cond.</td><td style="word-wrap: break-word;">lung cancer</td></tr>
<tr><td style="word-wrap: break-word;">format</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Output format. JSON returns a structured studies list; CSV returns raw text content.</td><td style="word-wrap: break-word;">json</td></tr>
<tr><td style="word-wrap: break-word;">fields</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Comma-separated list of fields to include in the response (when supported by the API).</td><td style="word-wrap: break-word;">protocolSection.identificationModule.briefTitle,hasResults</td></tr>
<tr><td style="word-wrap: break-word;">sort</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Sort order for results. Passed as sort (e.g., @relevance).</td><td style="word-wrap: break-word;">@relevance</td></tr>
<tr><td style="word-wrap: break-word;">count_total</td><td>False</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">If true, includes countTotal=true to request total count metadata.</td><td style="word-wrap: break-word;">true</td></tr>
<tr><td style="word-wrap: break-word;">other_terms</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Additional free-text terms. Sets query.term.</td><td style="word-wrap: break-word;">phase 3 randomized</td></tr>
<tr><td style="word-wrap: break-word;">location_terms</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Location-related search terms (country, city, etc.). Sets query.locn.</td><td style="word-wrap: break-word;">United States; California</td></tr>
<tr><td style="word-wrap: break-word;">title_acronym</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Search in study titles/acronyms. Sets query.titles.</td><td style="word-wrap: break-word;">KEYNOTE</td></tr>
<tr><td style="word-wrap: break-word;">intervention_treatment</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Filter by intervention or treatment. Sets query.incr.</td><td style="word-wrap: break-word;">Pembrolizumab</td></tr>
<tr><td style="word-wrap: break-word;">outcome_measures</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Filter by outcome measures. Sets query.outc.</td><td style="word-wrap: break-word;">overall survival</td></tr>
<tr><td style="word-wrap: break-word;">sponsor_collaborator</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Sponsor or collaborator name search. Sets query.spons.</td><td style="word-wrap: break-word;">National Cancer Institute (NCI)</td></tr>
<tr><td style="word-wrap: break-word;">lead_sponsor_name</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Lead sponsor name. Sets query.lead.</td><td style="word-wrap: break-word;">Merck Sharp & Dohme LLC</td></tr>
<tr><td style="word-wrap: break-word;">study_ids</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Search by one or more study IDs (e.g., NCT IDs). Sets query.id.</td><td style="word-wrap: break-word;">NCT04000165,NCT05012345</td></tr>
<tr><td style="word-wrap: break-word;">patient_search_area</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Patient-centric search area terms. Sets query.patient.</td><td style="word-wrap: break-word;">Boston, MA</td></tr>
<tr><td style="word-wrap: break-word;">filter_overallStatus</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Comma-separated overall status filters. Sets filter.overallStatus.</td><td style="word-wrap: break-word;">RECRUITING,ACTIVE,NOT RECRUITING</td></tr>
<tr><td style="word-wrap: break-word;">filter_ids</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Comma-separated list of IDs to filter. Sets filter.ids.</td><td style="word-wrap: break-word;">NCT04000165,NCT05012345</td></tr>
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
<tr><td style="word-wrap: break-word;">results</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Search results. For JSON format, a prettified JSON array of study objects; for CSV, the raw CSV text.</td><td style="word-wrap: break-word;">[{ "protocolSection": { "identificationModule": { "nctId": "NCT04000165" } } }]</td></tr>
<tr><td style="word-wrap: break-word;">metadata</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Metadata about the request/response. For JSON format, includes totalStudies, query, apiVersion; for CSV, includes format, content_length, and query.</td><td style="word-wrap: break-word;">{ "total_studies": 1234, "query": "lung cancer", "api_version": "2.0" }</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Format behavior**: With format=json, the node parses and returns the 'studies' array and constructs summary metadata; with CSV, it returns raw text content and basic metadata only.
- **Core query**: condition_or_disease maps to query.cond; many other inputs map to query.* or filter.* parameters supported by ClinicalTrials.gov v2.
- **Sorting**: Default sort is '@relevance'. Override by setting the sort input.
- **Counts**: Enabling count_total sets countTotal=true; total_studies is populated in JSON mode when the API returns it.
- **Fields selection**: The fields input is a comma-separated list passed through to the API; ensure requested fields exist in the v2 API.
- **Timeouts and errors**: The node uses a 30-second timeout and returns error strings on network or JSON parsing failures.
- **Status filters**: filter_overallStatus accepts comma-separated status values recognized by ClinicalTrials.gov (e.g., RECRUITING, COMPLETED).

## Troubleshooting
- **Empty results**: Broaden condition_or_disease or remove restrictive filters (e.g., intervention_treatment, filter_overallStatus).
- **Network error**: Verify internet connectivity and ClinicalTrials.gov availability; retry. The node returns '{"error": "Network error"}' in both outputs on failure.
- **JSON decode error**: Ensure format is set to 'json' when expecting parsed JSON. If the API returns malformed JSON, try again later or switch to CSV.
- **Unexpected fields missing**: Check that your fields list matches valid v2 field paths; remove invalid fields.
- **Large responses slow or truncated downstream**: Prefer JSON with specific fields to reduce payload size, or add additional filters to narrow results.
- **Sort not applied**: Confirm sort value syntax (e.g., '@relevance') and that it is supported by the API.
