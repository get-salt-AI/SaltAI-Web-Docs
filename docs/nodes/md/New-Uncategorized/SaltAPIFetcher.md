# API Fetcher

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Fetches data from healthcare-related APIs with optional presets and simple auth. Supports single GET requests or parallel batches, auto-detects response format, limits JSON list size, and includes retry and timeout handling.

</div>
<div style="flex: 0 0 300px;"><img src="../../../images/previews/New-Uncategorized/SaltAPIFetcher.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node to retrieve healthcare data such as FHIR resources or clinical datasets. Choose a preset for common endpoints or provide a custom URL. For multiple calls, switch to parallel mode and supply a JSON array of request configs; the node aggregates responses and provides an execution log.

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
<tr><td style="word-wrap: break-word;">preset_endpoint</td><td>True</td><td style="word-wrap: break-word;">enum ['custom', 'fhir_patients', 'fhir_observations', 'fhir_medications', 'fhir_conditions', 'fhir_diagnostic_reports', 'covid_tracker', 'drug_interaction_checker', 'medical_codes_icd10', 'clinical_trials']</td><td style="word-wrap: break-word;">Selects a predefined healthcare API endpoint or 'custom' to use your own URL.</td><td style="word-wrap: break-word;">fhir_patients</td></tr>
<tr><td style="word-wrap: break-word;">api_url</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Full API URL used when preset_endpoint is 'custom'. Ignored when a preset is selected.</td><td style="word-wrap: break-word;">https://hapi.fhir.org/baseR4/Patient</td></tr>
<tr><td style="word-wrap: break-word;">execution_mode</td><td>True</td><td style="word-wrap: break-word;">enum ['single', 'parallel']</td><td style="word-wrap: break-word;">Single request or parallel batch execution.</td><td style="word-wrap: break-word;">single</td></tr>
<tr><td style="word-wrap: break-word;">max_workers</td><td>False</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Concurrent worker count for parallel execution.</td><td style="word-wrap: break-word;">4</td></tr>
<tr><td style="word-wrap: break-word;">batch_requests</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">JSON array of request configs for parallel execution. Each item may include 'url' and 'params' (JSON string).</td><td style="word-wrap: break-word;">[{"url": "https://hapi.fhir.org/baseR4/Patient", "params": "{\"name\": \"Smith\"}"}, {"url": "https://api.covidtracking.com/v1/us/current.json"}]</td></tr>
<tr><td style="word-wrap: break-word;">auth_token</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Authentication token sent as Bearer Authorization header.</td><td style="word-wrap: break-word;">eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...</td></tr>
<tr><td style="word-wrap: break-word;">query_params</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">JSON object string of query parameters appended to the URL.</td><td style="word-wrap: break-word;">{"name": "John", "_count": 10}</td></tr>
<tr><td style="word-wrap: break-word;">max_items</td><td>False</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Maximum number of items to retain from JSON list responses (single mode).</td><td style="word-wrap: break-word;">10</td></tr>
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
<tr><td style="word-wrap: break-word;">response_data</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Fetched data as a string. JSON responses are pretty-printed and may be truncated to max_items; parallel mode returns a JSON array of per-request results.</td><td style="word-wrap: break-word;">{"resourceType": "Bundle", ...}</td></tr>
<tr><td style="word-wrap: break-word;">status_message</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Human-readable outcome summary for the request(s).</td><td style="word-wrap: break-word;">Successfully fetched 10 items from API</td></tr>
<tr><td style="word-wrap: break-word;">success</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">True if the request succeeded (parallel: true if at least one request succeeded).</td><td style="word-wrap: break-word;">True</td></tr>
<tr><td style="word-wrap: break-word;">status_code</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">HTTP status code for single requests; in parallel mode, returns 200 for the aggregated operation.</td><td style="word-wrap: break-word;">200</td></tr>
<tr><td style="word-wrap: break-word;">response_headers</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">JSON string of response headers (single). In parallel mode, contains a JSON object with 'execution_log'.</td><td style="word-wrap: break-word;">{"Content-Type": "application/json", ...}</td></tr>
<tr><td style="word-wrap: break-word;">response_time</td><td style="word-wrap: break-word;">FLOAT</td><td style="word-wrap: break-word;">Total elapsed time in seconds for the operation.</td><td style="word-wrap: break-word;">1.42</td></tr>
</tbody>
</table>
</div>

## Important Notes
- When preset_endpoint is not 'custom', the preset URL is used and the 'api_url' field is ignored.
- Only GET requests are supported via the main function. For parallel mode, each request uses GET.
- In parallel mode with a preset selected, the preset URL is applied to all batch items, ignoring each item's 'url'. Use 'custom' to honor per-item URLs.
- query_params must be a valid JSON object string; invalid JSON is ignored with a warning.
- batch_requests must be a valid JSON array string; invalid JSON fails the node with an error message.
- JSON list responses in single mode are truncated to 'max_items'. In parallel mode, an internal high limit is used per response.
- Retries with exponential backoff are applied (default 3 attempts, ~30s timeout per request).
- Authentication is simplified to a single Bearer token via 'auth_token' header.
- response_headers is serialized to a JSON string; in parallel mode it contains {"execution_log": [...]} rather than real response headers.
- success is true if the HTTP request(s) completed successfully; network or server errors return success=false and status_code=0 (single).

## Troubleshooting
- Invalid batch_requests JSON: Ensure the value is a JSON array string like [{"url": "https://...", "params": "{...}"}].
- Invalid query_params JSON: Provide a JSON object string (e.g., "{\"name\": \"John\"}").
- 401/403 Unauthorized: Verify 'auth_token' is correct and required by the endpoint.
- Timeout errors: Increase reliability by reducing batch size, increasing 'max_workers' sensibly, or trying again later. Default per-request timeout is about 30 seconds.
- Preset vs URL mismatch: If using a preset, the node ignores 'api_url' and (in parallel) each item's 'url'. Set preset_endpoint='custom' to use custom URLs.
- Empty response_data with error message: Check 'status_message' for details (e.g., network errors, invalid endpoint, or server errors).
- Unexpected response format: The node auto-detects format; if JSON parsing fails, data is returned as text and the status message will indicate formatting issues.
