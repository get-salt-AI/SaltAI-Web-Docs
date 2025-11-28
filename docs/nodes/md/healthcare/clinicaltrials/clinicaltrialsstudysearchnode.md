# ClinicalTrials Study Search

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Searches ClinicalTrials.gov (API v2) for studies matching a condition/disease and optional filters. Returns either a JSON-formatted list of studies plus metadata, or raw CSV content with basic metadata. Supports additional query terms (e.g., location, title, sponsor) and filters (e.g., overall status, IDs) with sorting and an option to request total counts.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/healthcare/clinicaltrials/clinicaltrialsstudysearchnode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node to retrieve clinical trial studies for a given condition or disease, optionally refining results by location, title/acronym, interventions, outcomes, sponsors, study IDs, or overall status. Typical workflow: provide a condition (e.g., 'diabetes'), choose output format (JSON recommended), optionally specify fields to include, add filters and sorting, then pass results to downstream nodes for parsing, display, or analysis.

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
<tr><td style="word-wrap: break-word;">condition_or_disease</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Primary search query for conditions or diseases. Mapped to query.cond.</td><td style="word-wrap: break-word;">type 2 diabetes</td></tr>
<tr><td style="word-wrap: break-word;">format</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Response format. JSON returns structured results; CSV returns raw text.</td><td style="word-wrap: break-word;">json</td></tr>
<tr><td style="word-wrap: break-word;">fields</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Comma-separated list of fields to include in the response. Applied when supported by the API.</td><td style="word-wrap: break-word;">protocolSection.identificationModule.briefTitle,protocolSection.statusModule.overallStatus</td></tr>
<tr><td style="word-wrap: break-word;">sort</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Sort order for results. Defaults to relevance.</td><td style="word-wrap: break-word;">@relevance</td></tr>
<tr><td style="word-wrap: break-word;">count_total</td><td>False</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">If true, requests total count of studies (API parameter countTotal=true).</td><td style="word-wrap: break-word;">true</td></tr>
<tr><td style="word-wrap: break-word;">other_terms</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Additional keyword terms. Mapped to query.term.</td><td style="word-wrap: break-word;">pediatric OR adolescent</td></tr>
<tr><td style="word-wrap: break-word;">location_terms</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Location-based query terms. Mapped to query.locn.</td><td style="word-wrap: break-word;">United States, California</td></tr>
<tr><td style="word-wrap: break-word;">title_acronym</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Match text in study titles or acronyms. Mapped to query.titles.</td><td style="word-wrap: break-word;">metformin</td></tr>
<tr><td style="word-wrap: break-word;">intervention_treatment</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Intervention/treatment terms. Mapped to query.incr.</td><td style="word-wrap: break-word;">insulin</td></tr>
<tr><td style="word-wrap: break-word;">outcome_measures</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Outcome measures terms. Mapped to query.outc.</td><td style="word-wrap: break-word;">HbA1c</td></tr>
<tr><td style="word-wrap: break-word;">sponsor_collaborator</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Sponsor or collaborator terms. Mapped to query.spons.</td><td style="word-wrap: break-word;">National Institutes of Health</td></tr>
<tr><td style="word-wrap: break-word;">lead_sponsor_name</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Lead sponsor name. Mapped to query.lead.</td><td style="word-wrap: break-word;">Pfizer</td></tr>
<tr><td style="word-wrap: break-word;">study_ids</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Study identifiers (e.g., NCT IDs). Mapped to query.id.</td><td style="word-wrap: break-word;">NCT01234567,NCT08976543</td></tr>
<tr><td style="word-wrap: break-word;">patient_search_area</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Patient-centered search area terms. Mapped to query.patient.</td><td style="word-wrap: break-word;">Boston, MA</td></tr>
<tr><td style="word-wrap: break-word;">filter_overallStatus</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Filter by overall status (comma-separated). Mapped to filter.overallStatus.</td><td style="word-wrap: break-word;">RECRUITING,ACTIVE,NOT_YET_RECRUITING</td></tr>
<tr><td style="word-wrap: break-word;">filter_ids</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Filter by IDs (comma-separated). Mapped to filter.ids.</td><td style="word-wrap: break-word;">NCT01234567,NCT07654321</td></tr>
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
<tr><td style="word-wrap: break-word;">results</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">If format=json: JSON string of the 'studies' array. If format=csv: raw CSV text.</td><td style="word-wrap: break-word;">[{ "protocolSection": { ... } }, { ... }]</td></tr>
<tr><td style="word-wrap: break-word;">metadata</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">If format=json: JSON string containing total_studies, query, api_version. If format=csv: JSON string with format, content_length, and query.</td><td style="word-wrap: break-word;">{"total_studies": 124, "query": "type 2 diabetes", "api_version": "2.0"}</td></tr>
</tbody>
</table>
</div>

## Important Notes
- Fields must be provided as a comma-separated list; unknown fields may be ignored by the API.
- Default sort is @relevance; use API-supported sort strings for other orderings.
- JSON format is recommended for structured downstream processing; CSV returns raw text.
- When count_total is true, total count is included in JSON metadata where supported by the API.
- This node queries ClinicalTrials.gov API v2 at the /studies endpoint with a 30-second timeout.
- Error responses are returned as JSON strings in both outputs with an 'error' key.

## Troubleshooting
- Network error: Ensure internet connectivity and that ClinicalTrials.gov is reachable; try again or reduce request size.
- JSON decode error: Use format=json only when the endpoint returns valid JSON; otherwise switch to CSV.
- Empty results: Check the condition_or_disease and filters; broaden search terms or remove restrictive filters.
- Invalid filters/sort: Verify values for filter_overallStatus, filter_ids, and sort match API expectations.
- Large response/timeout: Narrow the search or specify fields to reduce payload size; try again later.
