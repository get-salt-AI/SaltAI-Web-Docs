# API Fetcher

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Fetches data from healthcare-related APIs with optional presets for common FHIR and medical endpoints. Supports single or parallel request execution, simple bearer-token authentication, automatic response format detection (JSON/XML/text), basic retries, and status reporting.

</div>
<div style="flex: 0 0 300px;"><img src="../../../images/previews/utilities/saltapifetcher.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node to retrieve data from healthcare APIs in either single-request or batch/parallel mode. Select a preset endpoint for common resources (e.g., FHIR Patients), or choose custom to provide your own URL. Optionally add a bearer token and query parameters. In parallel mode, provide a JSON array of request configs to fan out multiple GET requests concurrently and aggregate the results.

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
<tr><td style="word-wrap: break-word;">preset_endpoint</td><td>True</td><td style="word-wrap: break-word;">CHOICE</td><td style="word-wrap: break-word;">Select a predefined healthcare API endpoint or 'custom' to use your own API URL.</td><td style="word-wrap: break-word;">fhir_patients</td></tr>
<tr><td style="word-wrap: break-word;">api_url</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The API URL to fetch data from. Used only when preset_endpoint is 'custom'.</td><td style="word-wrap: break-word;">https://hapi.fhir.org/baseR4/Patient</td></tr>
<tr><td style="word-wrap: break-word;">execution_mode</td><td>True</td><td style="word-wrap: break-word;">CHOICE</td><td style="word-wrap: break-word;">Choose 'single' for one request or 'parallel' to execute multiple requests concurrently.</td><td style="word-wrap: break-word;">single</td></tr>
<tr><td style="word-wrap: break-word;">max_workers</td><td>False</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Number of concurrent workers when execution_mode is 'parallel'.</td><td style="word-wrap: break-word;">4</td></tr>
<tr><td style="word-wrap: break-word;">batch_requests</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">A JSON array describing multiple GET requests for parallel mode. Each item may contain 'url' and optional 'params' (JSON string of query parameters).</td><td style="word-wrap: break-word;">[{"url": "https://hapi.fhir.org/baseR4/Patient", "params": "{\"_count\": 5}"}, {"url": "https://api.covidtracking.com/v1/us/current.json"}]</td></tr>
<tr><td style="word-wrap: break-word;">auth_token</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Bearer token for Authorization header. Leave empty if not required by the API.</td><td style="word-wrap: break-word;"><bearer-token></td></tr>
<tr><td style="word-wrap: break-word;">query_params</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Query parameters as a JSON object string. Applied to the request URL. In parallel mode, per-request 'params' in batch_requests take precedence.</td><td style="word-wrap: break-word;">{"name": "John", "_count": 10}</td></tr>
<tr><td style="word-wrap: break-word;">max_items</td><td>False</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Maximum number of items to keep when the response is a JSON array (single mode).</td><td style="word-wrap: break-word;">10</td></tr>
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
<tr><td style="word-wrap: break-word;">response_data</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The fetched and processed response data as a string. For JSON responses, formatted JSON is returned. In parallel mode, this is a JSON array of each request’s processed data.</td><td style="word-wrap: break-word;">{"resourceType": "Bundle", "entry": [...]}</td></tr>
<tr><td style="word-wrap: break-word;">status_message</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Human-readable summary of the operation, including success counts in parallel mode.</td><td style="word-wrap: break-word;">Successfully fetched 10 items from API</td></tr>
<tr><td style="word-wrap: break-word;">success</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">Indicates if the operation succeeded. In parallel mode, true if at least one request succeeded.</td><td style="word-wrap: break-word;">True</td></tr>
<tr><td style="word-wrap: break-word;">status_code</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">HTTP status code for single requests. In parallel mode, returns 200 for the aggregated result.</td><td style="word-wrap: break-word;">200</td></tr>
<tr><td style="word-wrap: break-word;">response_headers</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Response headers as a JSON string. In parallel mode, contains an 'execution_log' array with per-request outcomes.</td><td style="word-wrap: break-word;">{"Content-Type": "application/fhir+json"}</td></tr>
<tr><td style="word-wrap: break-word;">response_time</td><td style="word-wrap: break-word;">FLOAT</td><td style="word-wrap: break-word;">Total elapsed time in seconds for the operation.</td><td style="word-wrap: break-word;">1.42</td></tr>
</tbody>
</table>
</div>

## Important Notes
- Preset endpoints include: fhir_patients, fhir_observations, fhir_medications, fhir_conditions, fhir_diagnostic_reports, covid_tracker, drug_interaction_checker, medical_codes_icd10, clinical_trials.
- api_url is only used when preset_endpoint is 'custom'.
- Only GET requests are supported in this simplified interface; use batch_requests for multiple GETs in parallel.
- Response format is auto-detected (JSON/XML/text). JSON arrays are truncated to max_items in single mode.
- Parallel mode aggregates results and sets success to true if any request succeeds; status_code is set to 200 for the aggregate.
- Default timeout per request is approximately 30 seconds with basic retry logic (up to 3 attempts).
- query_params and per-item 'params' in batch_requests must be valid JSON object strings (e.g., "{\"_count\": 10}").
- For protected APIs, provide auth_token as a bearer token. Never embed secrets directly in flows that may be shared.
- When handling patient or clinical data, ensure appropriate compliance and safeguards.

## Troubleshooting
- Invalid batch_requests: Ensure it is a JSON array string. Example: "[{\"url\": \"https://...\"}]".
- Invalid query_params or params: Provide a valid JSON object string (e.g., "{\"_count\": 10}"), not URL-encoded or Python dict syntax.
- Authentication errors (401/403): Verify auth_token, endpoint requirements, and scopes. Some endpoints may not require authentication.
- Timeouts: Large responses or slow endpoints can exceed ~30s per request. Reduce max_items, narrow query parameters, or decrease batch size.
- Unexpected response format: If non-JSON is returned, the node outputs raw text or XML. Verify endpoint and parameters.
- Empty or partial results in parallel mode: Check the execution_log in response_headers for per-request messages and errors.
- Custom URL ignored: This occurs if preset_endpoint is not 'custom'. Set preset_endpoint='custom' to use api_url.
