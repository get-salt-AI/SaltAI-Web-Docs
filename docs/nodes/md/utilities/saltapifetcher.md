# API Fetcher

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Fetches data from healthcare-focused APIs with optional presets for common FHIR and medical endpoints. Supports single-call or parallel batch execution, simple Bearer token authentication, automatic response format detection (JSON/XML/Text), and built-in retry with basic error handling.

</div>
<div style="flex: 0 0 300px;"><img src="../../../images/previews/utilities/saltapifetcher.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node to retrieve healthcare data from preset endpoints (e.g., FHIR Patients) or a custom URL. For a single call, select a preset or set a custom URL, optionally add an auth token and JSON query parameters, and run. For multiple calls, switch to parallel mode and provide batch requests as a JSON array; the node will execute them concurrently and aggregate the results.

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
<tr><td style="word-wrap: break-word;">preset_endpoint</td><td>True</td><td style="word-wrap: break-word;">CHOICE</td><td style="word-wrap: break-word;">Select a predefined healthcare API endpoint or 'custom' to use your own URL.</td><td style="word-wrap: break-word;">fhir_patients</td></tr>
<tr><td style="word-wrap: break-word;">api_url</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Full API URL to fetch from. Only used when preset_endpoint is 'custom'.</td><td style="word-wrap: break-word;">https://hapi.fhir.org/baseR4/Patient</td></tr>
<tr><td style="word-wrap: break-word;">execution_mode</td><td>True</td><td style="word-wrap: break-word;">CHOICE</td><td style="word-wrap: break-word;">Select 'single' for one request or 'parallel' to execute multiple requests concurrently.</td><td style="word-wrap: break-word;">single</td></tr>
<tr><td style="word-wrap: break-word;">max_workers</td><td>False</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Number of concurrent workers used in parallel execution.</td><td style="word-wrap: break-word;">4</td></tr>
<tr><td style="word-wrap: break-word;">batch_requests</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">JSON array describing multiple requests for parallel mode. Each item supports 'url' and optional 'params' (JSON object as string). If a preset is selected (not 'custom'), the preset URL is used and 'url' in items is ignored.</td><td style="word-wrap: break-word;">[{"url": "https://api.example.com/resource", "params": "{\"id\": \"123\"}"}, {"url": "https://api.example.com/other", "params": "{\"name\": \"John\"}"}]</td></tr>
<tr><td style="word-wrap: break-word;">auth_token</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Authentication token included as a Bearer token in the Authorization header.</td><td style="word-wrap: break-word;"><api-token></td></tr>
<tr><td style="word-wrap: break-word;">query_params</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Query parameters as a JSON object string. Applied to the request URL. In parallel mode, each batch item can override with its own 'params'.</td><td style="word-wrap: break-word;">{"name": "John", "_count": 10}</td></tr>
<tr><td style="word-wrap: break-word;">max_items</td><td>False</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Maximum number of items to return when the API response is a JSON array (single mode only).</td><td style="word-wrap: break-word;">10</td></tr>
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
<tr><td style="word-wrap: break-word;">response_data</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Processed response content as a string. JSON responses are pretty-printed; parallel mode returns a JSON stringified array of per-request results.</td><td style="word-wrap: break-word;">[   "{\n  \"id\": \"123\", ...\n}",   "{\n  \"id\": \"456\", ...\n}" ]</td></tr>
<tr><td style="word-wrap: break-word;">status_message</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Human-readable status message summarizing the outcome (e.g., number of items fetched or parallel success/failure counts).</td><td style="word-wrap: break-word;">Executed 2 requests in parallel. Success: 2, Failed: 0</td></tr>
<tr><td style="word-wrap: break-word;">success</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">Indicates whether the request(s) succeeded. In parallel mode, true if at least one request succeeded.</td><td style="word-wrap: break-word;">True</td></tr>
<tr><td style="word-wrap: break-word;">status_code</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">HTTP status code. In parallel mode, returns 200 for the aggregated result; individual request codes are not surfaced in this field.</td><td style="word-wrap: break-word;">200</td></tr>
<tr><td style="word-wrap: break-word;">response_headers</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Response headers as a JSON string. In parallel mode, contains an 'execution_log' array with per-request logs.</td><td style="word-wrap: break-word;">{"execution_log": ["Request 0: SUCCESS - Successfully fetched ...", "Request 1: SUCCESS - Successfully fetched ..."]}</td></tr>
<tr><td style="word-wrap: break-word;">response_time</td><td style="word-wrap: break-word;">FLOAT</td><td style="word-wrap: break-word;">Total time taken to complete the operation in seconds.</td><td style="word-wrap: break-word;">1.42</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Presets available**: fhir_patients, fhir_observations, fhir_medications, fhir_conditions, fhir_diagnostic_reports, covid_tracker, drug_interaction_checker, medical_codes_icd10, clinical_trials.
- **Custom URL usage**: The api_url field is only used when preset_endpoint is 'custom'.
- **Auth handling**: auth_token is sent as 'Authorization: Bearer <token>'. Leave empty for public endpoints.
- **Query params format**: query_params must be a JSON object string (e.g., "{\"name\": \"John\"}"). Invalid JSON is ignored.
- **Parallel mode input**: batch_requests must be a JSON array. Each item supports 'url' and optional 'params' (as a JSON object string). If using a preset (not 'custom'), the preset URL is applied to all items and item 'url' values are ignored.
- **Item limiting**: max_items only affects single mode when the response is a JSON array. In parallel mode, each response is processed with a high limit to preserve results.
- **Retries and timeouts**: Each request uses up to 3 retries with exponential backoff and a 30s timeout. Network errors return status_code 0.
- **Response detection**: The node auto-detects response format (JSON/XML/Text). Non-JSON content is returned as text.
- **Compliance reminder**: Ensure proper authorization and handling when accessing patient data or regulated endpoints.

## Troubleshooting
- **Invalid batch_requests JSON**: Ensure batch_requests is valid JSON and an array. Example: "[{\"url\": \"https://...\", \"params\": \"{\\\"id\\\": \\\"123\\\"}\"}]".
- **Invalid query_params**: Provide a JSON object string. If invalid, parameters are ignored and the base URL is used.
- **401/403 errors**: Verify auth_token is present and valid for protected endpoints.
- **Status code 0 and errors**: Indicates network/transport errors or timeouts. Check connectivity, endpoint availability, and consider reducing max_workers or request volume.
- **Parallel mode does nothing**: Ensure execution_mode is 'parallel' and batch_requests is non-empty and valid JSON.
- **Unexpected data format**: If the response is not valid JSON, it will be returned as text. Check endpoint and parameters.
- **Preset vs. custom confusion**: When using a preset (not 'custom'), api_url and batch item 'url' values are ignored; only the preset URL is used.
