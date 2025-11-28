# API Fetcher

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Fetches data from healthcare-focused APIs with optional presets for common FHIR and medical endpoints. Supports single or parallel requests, simple Bearer token authentication, automatic response format detection (JSON/XML/text), and basic retry handling. Returns response data, status information, headers, and timing metrics.

</div>
<div style="flex: 0 0 300px;"><img src="../../../images/previews/utilities/saltapifetcher.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node to retrieve healthcare data (e.g., FHIR resources or clinical trial data) either as a one-off request or as multiple requests executed in parallel. Select a preset endpoint or provide a custom URL, optionally add an auth token and JSON-formatted query parameters, and set max items to limit large JSON arrays. In parallel mode, supply a JSON array of request configs to fan out multiple calls and aggregate results.

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
<tr><td style="word-wrap: break-word;">preset_endpoint</td><td>True</td><td style="word-wrap: break-word;">CHOICE</td><td style="word-wrap: break-word;">Select a preconfigured healthcare API or choose 'custom' to use your own URL. Presets include: fhir_patients, fhir_observations, fhir_medications, fhir_conditions, fhir_diagnostic_reports, covid_tracker, drug_interaction_checker, medical_codes_icd10, clinical_trials.</td><td style="word-wrap: break-word;">fhir_patients</td></tr>
<tr><td style="word-wrap: break-word;">api_url</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The API URL to fetch data from when 'preset_endpoint' is set to 'custom'. Ignored for non-custom presets.</td><td style="word-wrap: break-word;">https://hapi.fhir.org/baseR4/Patient</td></tr>
<tr><td style="word-wrap: break-word;">execution_mode</td><td>True</td><td style="word-wrap: break-word;">CHOICE</td><td style="word-wrap: break-word;">Choose 'single' for a single request or 'parallel' to execute multiple requests concurrently using 'batch_requests' and 'max_workers'.</td><td style="word-wrap: break-word;">single</td></tr>
<tr><td style="word-wrap: break-word;">max_workers</td><td>False</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Number of concurrent workers for parallel execution. Used only when execution_mode is 'parallel'.</td><td style="word-wrap: break-word;">4</td></tr>
<tr><td style="word-wrap: break-word;">batch_requests</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">JSON array describing multiple requests for parallel mode. Each item can include 'url' and optional 'params' (JSON object string). If a preset is selected (not 'custom'), 'url' can be omitted and the preset URL is used.</td><td style="word-wrap: break-word;">[{"url": "https://hapi.fhir.org/baseR4/Patient", "params": "{\"_count\": 5}"}, {"url": "https://clinicaltrials.gov/api/v2/studies", "params": "{\"query\": \"diabetes\"}"}]</td></tr>
<tr><td style="word-wrap: break-word;">auth_token</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Authentication token sent as a Bearer token in the Authorization header.</td><td style="word-wrap: break-word;"><api-token></td></tr>
<tr><td style="word-wrap: break-word;">query_params</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">JSON object string of query parameters merged into the request URL. Applies to single request mode and as default params for batch items that omit 'params'.</td><td style="word-wrap: break-word;">{"_count": 10, "name": "John"}</td></tr>
<tr><td style="word-wrap: break-word;">max_items</td><td>False</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Maximum number of items to return when the response body is a JSON array (single request mode only).</td><td style="word-wrap: break-word;">10</td></tr>
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
<tr><td style="word-wrap: break-word;">response_data</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The fetched data as a string. Auto-formatted for JSON (pretty-printed), or raw text/XML depending on response.</td><td style="word-wrap: break-word;">{   "resourceType": "Bundle",   "entry": [ ... ] }</td></tr>
<tr><td style="word-wrap: break-word;">status_message</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Human-readable status of the fetch, including item counts or errors.</td><td style="word-wrap: break-word;">Successfully fetched 10 items from API</td></tr>
<tr><td style="word-wrap: break-word;">success</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">Indicates whether the request(s) completed successfully.</td><td style="word-wrap: break-word;">True</td></tr>
<tr><td style="word-wrap: break-word;">status_code</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">HTTP status code for the request. In parallel mode, returns 200 if at least one request succeeded.</td><td style="word-wrap: break-word;">200</td></tr>
<tr><td style="word-wrap: break-word;">response_headers</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Response headers as a JSON string. In parallel mode, contains an execution log JSON object.</td><td style="word-wrap: break-word;">{"content-type": "application/fhir+json;charset=utf-8"}</td></tr>
<tr><td style="word-wrap: break-word;">response_time</td><td style="word-wrap: break-word;">FLOAT</td><td style="word-wrap: break-word;">Total elapsed time in seconds for the operation.</td><td style="word-wrap: break-word;">0.84</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Preset handling**: If 'preset_endpoint' is not 'custom', the preset URL is used and 'api_url' is ignored.
- **Query parameters**: 'query_params' must be a valid JSON object string (e.g., "{\"_count\": 10}"). Invalid JSON is ignored with a warning.
- **Authentication**: 'auth_token' is included as 'Authorization: Bearer <token>'. No other auth types are supported in this simplified interface.
- **Parallel mode inputs**: 'batch_requests' must be a JSON array. Each item supports 'url' and 'params' (JSON object string).
- **Timeouts and retries**: Each request uses a ~30s timeout and basic retry logic (up to 3 attempts) on transient errors.
- **Response processing**: The node auto-detects response format. JSON arrays may be truncated to 'max_items' in single mode.
- **Headers output**: For single requests, headers are returned as a JSON string. For parallel execution, 'response_headers' contains a JSON object with an 'execution_log' array.
- **Success in parallel**: 'success' is true if at least one request succeeds; review the execution log for per-request outcomes.
- **HIPAA considerations**: When fetching patient-related data, ensure appropriate authorization and compliant handling of sensitive information.

## Troubleshooting
- **Invalid query_params**: If query parameters are not valid JSON, they will be ignored. Provide a proper JSON object string (e.g., "{\"_count\": 10}").
- **Invalid batch_requests**: Ensure 'batch_requests' is a JSON array string. Each element should include at least 'url' (for custom) or rely on a preset plus optional 'params'.
- **Unauthorized or 401/403**: Verify 'auth_token' is correct and has necessary scopes. Place the literal token value in the field (e.g., "<api-token>").
- **Timeouts**: Large or slow endpoints may exceed the ~30s timeout. Reduce payload via query params, lower 'max_items', or split requests in parallel.
- **Mixed outcomes in parallel**: If some requests fail, 'success' may still be true. Inspect 'response_headers' (execution_log) and individual result entries in 'response_data'.
- **Wrong endpoint used**: When using a preset, 'api_url' is ignored. Set 'preset_endpoint' to 'custom' to use your custom URL.
- **Unexpected text/XML**: If JSON is expected but the API returns text/XML, verify endpoint and headers. The node does not force content-type.
