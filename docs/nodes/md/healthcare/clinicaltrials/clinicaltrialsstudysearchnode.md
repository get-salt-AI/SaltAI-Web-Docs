# ClinicalTrials Study Search

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Searches ClinicalTrials.gov (API v2) for studies matching a condition or disease, with rich filtering and sorting options. Returns either structured JSON results or raw CSV text, along with compact metadata about the query and response.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/healthcare/clinicaltrials/clinicaltrialsstudysearchnode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node to programmatically search and filter clinical study listings from ClinicalTrials.gov. Provide a primary condition/disease term, then refine with optional filters (e.g., location, interventions, sponsors) and sorting. Typically used early in a workflow to fetch a study set for downstream analysis, summarization, or visualization.

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
<tr><td style="word-wrap: break-word;">condition_or_disease</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Primary search term for the medical condition or disease to query against.</td><td style="word-wrap: break-word;">Type 2 Diabetes</td></tr>
<tr><td style="word-wrap: break-word;">format</td><td>True</td><td style="word-wrap: break-word;">ENUM</td><td style="word-wrap: break-word;">Response format for results. JSON returns structured studies; CSV returns raw text.</td><td style="word-wrap: break-word;">json</td></tr>
<tr><td style="word-wrap: break-word;">fields</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Comma-separated list of fields to include in the response. Leave blank to use API defaults.</td><td style="word-wrap: break-word;">NCTId,BriefTitle,OverallStatus,Condition,LocationCountry</td></tr>
<tr><td style="word-wrap: break-word;">sort</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Sort order for returned studies. Defaults to relevance.</td><td style="word-wrap: break-word;">@relevance</td></tr>
<tr><td style="word-wrap: break-word;">count_total</td><td>False</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">If true, requests the API to include the total number of matching studies in the response metadata.</td><td style="word-wrap: break-word;">true</td></tr>
<tr><td style="word-wrap: break-word;">other_terms</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Additional free-text search terms (beyond the primary condition/disease).</td><td style="word-wrap: break-word;">metformin OR GLP-1</td></tr>
<tr><td style="word-wrap: break-word;">location_terms</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Location-based search terms (e.g., city, state, country).</td><td style="word-wrap: break-word;">United States</td></tr>
<tr><td style="word-wrap: break-word;">title_acronym</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Search within study titles and acronyms.</td><td style="word-wrap: break-word;">DAPA-HF</td></tr>
<tr><td style="word-wrap: break-word;">intervention_treatment</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Filter by intervention or treatment names.</td><td style="word-wrap: break-word;">semaglutide</td></tr>
<tr><td style="word-wrap: break-word;">outcome_measures</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Filter by outcome measure terms.</td><td style="word-wrap: break-word;">HbA1c reduction</td></tr>
<tr><td style="word-wrap: break-word;">sponsor_collaborator</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Filter by sponsor or collaborator names.</td><td style="word-wrap: break-word;">National Institutes of Health</td></tr>
<tr><td style="word-wrap: break-word;">lead_sponsor_name</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Filter by lead sponsor organization name.</td><td style="word-wrap: break-word;">Novo Nordisk</td></tr>
<tr><td style="word-wrap: break-word;">study_ids</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Filter by specific study identifiers (e.g., NCT IDs). Comma-separated.</td><td style="word-wrap: break-word;">NCT04000165,NCT05012345</td></tr>
<tr><td style="word-wrap: break-word;">patient_search_area</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Patient-specific location search area, typically used to find nearby studies.</td><td style="word-wrap: break-word;">Boston, MA</td></tr>
<tr><td style="word-wrap: break-word;">filter_overallStatus</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Overall study status filter. Comma-separated list of statuses.</td><td style="word-wrap: break-word;">AVAILABLE,COMPLETED</td></tr>
<tr><td style="word-wrap: break-word;">filter_ids</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Filter by predefined ID groups or facets if applicable. Comma-separated.</td><td style="word-wrap: break-word;">CTGClassification:Interventional</td></tr>
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
<tr><td style="word-wrap: break-word;">results</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Study search results. In JSON format, this is a pretty-printed JSON string of studies; in CSV format, the raw CSV text.</td><td style="word-wrap: break-word;">[{ "nctId": "NCT04000165", "briefTitle": "Example Study", "overallStatus": "COMPLETED" }]</td></tr>
<tr><td style="word-wrap: break-word;">metadata</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Summary metadata about the response, such as total studies (if requested), query term, API version, format, and content length (for non-JSON).</td><td style="word-wrap: break-word;">{ "total_studies": 124, "query": "Type 2 Diabetes", "api_version": "2.0" }</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **API Source**: Uses ClinicalTrials.gov API v2 /studies endpoint.
- **Result format**: With format=json, results are a JSON array of studies; with format=csv, results is raw CSV text.
- **Fields filtering**: Providing a fields list limits which fields are included; ensure field names match those supported by the API.
- **Sorting**: Default sort is by relevance; you can supply other sort directives supported by the API.
- **Counts**: Set count_total=true to request total result counts in metadata.
- **Status filtering**: filter_overallStatus accepts comma-separated statuses (e.g., AVAILABLE, COMPLETED).
- **Error handling**: On network or parsing errors, the node returns simple JSON error strings in both outputs.

## Troubleshooting
- **Empty results**: Broaden the condition_or_disease term or remove restrictive filters like study_ids or outcome_measures.
- **Unexpected fields**: If fields are blank in results, verify the field names are valid for the API and spelled correctly.
- **No total count**: Set count_total to true and use format=json to receive total_studies in metadata.
- **CSV parsing issues**: When format=csv, the results output is raw text; ensure downstream nodes expect and parse CSV.
- **Network errors**: Check connectivity and retry; the node times out requests after a short interval and returns a network error JSON string.
- **Status filter mismatch**: If filtering by status yields no results, verify the statuses are valid and comma-separated without extra spaces.
