# ClinicalTrials Study Search

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Searches ClinicalTrials.gov (API v2) for studies matching a condition or disease, with rich filtering and sorting options. Returns either structured JSON results or raw CSV content along with metadata about the query.

</div>
<div style="flex: 0 0 300px;"><img src="../../../images/previews/New-Uncategorized/ClinicalTrialsStudySearchNode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node to discover clinical studies by condition (e.g., lung cancer) and refine results with optional terms like location, intervention, sponsors, and status filters. It typically feeds downstream nodes that analyze or display study lists; select JSON for programmatic processing or CSV for spreadsheet-style handling.

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
<tr><td style="word-wrap: break-word;">condition_or_disease</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Primary search query for the condition or disease.</td><td style="word-wrap: break-word;">lung cancer</td></tr>
<tr><td style="word-wrap: break-word;">format</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Response format. Use 'json' for structured results or 'csv' for raw CSV text.</td><td style="word-wrap: break-word;">json</td></tr>
<tr><td style="word-wrap: break-word;">fields</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Comma-separated list of fields to include in the response. Leave empty to use API defaults.</td><td style="word-wrap: break-word;">NCTId, BriefTitle, Condition</td></tr>
<tr><td style="word-wrap: break-word;">sort</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Sorting directive. '@relevance' by default; supports API-supported sort expressions.</td><td style="word-wrap: break-word;">@relevance</td></tr>
<tr><td style="word-wrap: break-word;">count_total</td><td>False</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">If true, requests the API to include total study count (when supported by the endpoint and format).</td><td style="word-wrap: break-word;">true</td></tr>
<tr><td style="word-wrap: break-word;">other_terms</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Additional free-text terms to include in the query.</td><td style="word-wrap: break-word;">phase 3 randomized</td></tr>
<tr><td style="word-wrap: break-word;">location_terms</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Location-based query terms such as country, state, city, or facility names.</td><td style="word-wrap: break-word;">United States; California</td></tr>
<tr><td style="word-wrap: break-word;">title_acronym</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Match on study titles or acronyms.</td><td style="word-wrap: break-word;">KEYNOTE</td></tr>
<tr><td style="word-wrap: break-word;">intervention_treatment</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Filter by intervention or treatment terms.</td><td style="word-wrap: break-word;">Pembrolizumab</td></tr>
<tr><td style="word-wrap: break-word;">outcome_measures</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Filter by outcome measure terms.</td><td style="word-wrap: break-word;">overall survival</td></tr>
<tr><td style="word-wrap: break-word;">sponsor_collaborator</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Filter by sponsor or collaborator names.</td><td style="word-wrap: break-word;">Merck Sharp & Dohme LLC</td></tr>
<tr><td style="word-wrap: break-word;">lead_sponsor_name</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Filter by lead sponsor name.</td><td style="word-wrap: break-word;">National Cancer Institute (NCI)</td></tr>
<tr><td style="word-wrap: break-word;">study_ids</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Comma-separated list of study identifiers to match (e.g., NCT IDs).</td><td style="word-wrap: break-word;">NCT04000165,NCT04512345</td></tr>
<tr><td style="word-wrap: break-word;">patient_search_area</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Patient-centric search specifier (API-supported patient search area text).</td><td style="word-wrap: break-word;">within 50 miles of 94103</td></tr>
<tr><td style="word-wrap: break-word;">filter_overallStatus</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Comma-separated overall status filters.</td><td style="word-wrap: break-word;">AVAILABLE,COMPLETED</td></tr>
<tr><td style="word-wrap: break-word;">filter_ids</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Additional filter IDs supported by the API (comma-separated).</td><td style="word-wrap: break-word;">hasUsFacility,hasResults</td></tr>
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
<tr><td style="word-wrap: break-word;">results</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The study search results. For format=json, a JSON string of the 'studies' array; for format=csv, the raw CSV text.</td><td style="word-wrap: break-word;">[{ "protocolSection": { ... } }, ...] or "NCTId,BriefTitle,...\nNCT04000165,..."</td></tr>
<tr><td style="word-wrap: break-word;">metadata</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Query metadata as a JSON string. Includes fields like total_studies, query, api_version (for JSON), or format and content_length (for CSV).</td><td style="word-wrap: break-word;">{ "total_studies": 128, "query": "lung cancer", "api_version": "2.0" }</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Response format**: Only 'json' and 'csv' are supported. Choose 'json' for structured processing and 'csv' for raw text.
- **Field selection**: Providing 'fields' limits the payload to specified fields; leave blank to use API defaults.
- **Sorting and counts**: The 'sort' and 'count_total' options depend on API support and may influence performance.
- **Filters composition**: Multiple filters can be combined; ensure values follow ClinicalTrials.gov API conventions (e.g., comma-separated lists).
- **Rate limits and stability**: External API availability or rate limiting may affect responses.

## Troubleshooting
- **Network error**: Check internet connectivity, firewall settings, or retry later if the ClinicalTrials.gov API is unavailable.
- **Empty results**: Broaden 'condition_or_disease', remove restrictive filters, or verify field/filter names align with API capabilities.
- **Unexpected CSV content**: Confirm 'format' is set to 'csv' and that downstream nodes expect raw text, not parsed JSON.
- **JSON decode error**: Ensure 'format' is 'json' and that the response isn't an HTML or error page; inspect 'metadata' for clues.
- **Large responses/timeouts**: Reduce scope by narrowing 'fields', using fewer filters, or try again with a longer timeout upstream if configurable.
