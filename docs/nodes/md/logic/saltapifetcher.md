# API Fetcher

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

SaltAPIFetcher is a streamlined API client focused on healthcare and medical data sources, but it can call any HTTP GET endpoint. It supports single and parallel execution, with presets for popular FHIR resources and other clinical/public health APIs. The node auto-detects response format, can limit large JSON lists, and returns data along with status, headers, and timing metadata for reliable downstream processing.

</div>
<div style="flex: 0 0 300px;"><img src="../../../images/previews/logic/saltapifetcher.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node whenever you need to pull external API data—especially FHIR or medical datasets—into a Salt workflow. Typical scenarios include querying FHIR servers (Patients, Observations, Medications, Conditions, DiagnosticReports) for analytics or summarization; calling public health and clinical APIs such as covid_tracker, drug_interaction_checker, medical_codes_icd10, and clinical_trials; or hitting arbitrary GET endpoints via preset_endpoint="custom" combined with api_url. For fan-out patterns (e.g., multiple patient IDs or clinical trial IDs), switch execution_mode to "parallel" and provide batch_requests as a JSON array of request configurations to execute them concurrently. In a standard pipeline, upstream nodes supply user choices and secrets (endpoint selection, query filters, auth_token), SaltAPIFetcher performs the HTTP calls, and downstream nodes like SaltJSONListProcessor, SaltCodeExecutor, or LLM/analytics nodes parse, transform, or summarize the returned data. Prefer presets for common healthcare APIs to avoid manual URL mistakes, keep max_items modest when exploring, start with a low max_workers in parallel mode to avoid rate limits, and always inject auth_token from secure configuration rather than hard-coding it.

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
<tr><td style="word-wrap: break-word;">preset_endpoint</td><td>True</td><td style="word-wrap: break-word;">CHOICE</td><td style="word-wrap: break-word;">Chooses a predefined healthcare/medical API base URL or enables full custom mode. Options: "custom", "fhir_patients", "fhir_observations", "fhir_medications", "fhir_conditions", "fhir_diagnostic_reports", "covid_tracker", "drug_interaction_checker", "medical_codes_icd10", "clinical_trials". When set to anything other than "custom", the corresponding preset URL is used and api_url is ignored.</td><td style="word-wrap: break-word;">fhir_patients</td></tr>
<tr><td style="word-wrap: break-word;">api_url</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Full HTTP(S) URL to fetch from when preset_endpoint is "custom". Must be a valid GET endpoint that returns text, JSON, or XML. Ignored if a non-custom preset is selected.</td><td style="word-wrap: break-word;">https://hapi.fhir.org/baseR4/Patient</td></tr>
<tr><td style="word-wrap: break-word;">execution_mode</td><td>True</td><td style="word-wrap: break-word;">CHOICE</td><td style="word-wrap: break-word;">Controls whether a single request or multiple requests in parallel are executed. "single": perform one GET to the chosen URL (plus query_params). "parallel": execute multiple GET requests concurrently as defined in batch_requests.</td><td style="word-wrap: break-word;">single</td></tr>
<tr><td style="word-wrap: break-word;">max_workers</td><td>False</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Maximum number of concurrent worker threads in parallel mode. Only used when execution_mode is "parallel". Valid range is 1–10. Higher values increase concurrency but may stress external APIs or run into rate limits.</td><td style="word-wrap: break-word;">4</td></tr>
<tr><td style="word-wrap: break-word;">batch_requests</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">JSON array string describing multiple requests for parallel execution. Each element should be an object with at least a "url" field and optionally a "params" field. In the simplified public interface, all requests are HTTP GET and share the global auth_token; request bodies and custom per-request headers are not used. The "params" field, if provided, must itself be a JSON object string (for example: "{\"id\":\"123\"}") and is turned into query parameters.</td><td style="word-wrap: break-word;">[{"url": "https://hapi.fhir.org/baseR4/Patient", "params": "{\"name\":\"Smith\"}"}, {"url": "https://api.covidtracking.com/v1/us/current.json", "params": "{}"}]</td></tr>
<tr><td style="word-wrap: break-word;">auth_token</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Authentication token included as an HTTP Authorization header in the form "Bearer <token>". This can represent an API key, bearer token, or JWT depending on the upstream API. Leave empty for public or unauthenticated endpoints.</td><td style="word-wrap: break-word;"><my-api-bearer-token></td></tr>
<tr><td style="word-wrap: break-word;">query_params</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">JSON object encoded as a string specifying query parameters for the request in single mode (and as a default for batch requests that do not provide their own params). Keys and values are cast to strings and merged into the URL’s query string. Invalid JSON is ignored and logged as a warning; the request still runs without those parameters.</td><td style="word-wrap: break-word;">{"name": "John", "_count": 10}</td></tr>
<tr><td style="word-wrap: break-word;">max_items</td><td>False</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Maximum number of items to retain when the API response body is a JSON list in single mode. Must be within 1–100. If the list is longer, it is truncated to this length and the status_message notes that it was limited.</td><td style="word-wrap: break-word;">10</td></tr>
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
<tr><td style="word-wrap: break-word;">response_data</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The processed response body as text. For JSON responses, this is pretty-printed JSON (possibly truncated in single mode or aggregated in parallel mode). For XML or plain text, the original content is returned as-is. In parallel mode, this is a JSON array string of per-request data objects, ordered by completion.</td><td style="word-wrap: break-word;">{   "resourceType": "Bundle",   "type": "searchset",   "total": 10,   "entry": [     {       "resource": {         "resourceType": "Patient",         "id": "123",         "name": [           {"family": "Smith"}         ]       }     }   ] }</td></tr>
<tr><td style="word-wrap: break-word;">status_message</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Readable summary of the outcome. In single mode it indicates if a list or single object was fetched and how many items are present (or that JSON was invalid). In parallel mode it reports how many requests succeeded and failed. On errors, it contains a descriptive error message.</td><td style="word-wrap: break-word;">Executed 5 requests in parallel. Success: 4, Failed: 1</td></tr>
<tr><td style="word-wrap: break-word;">success</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">Overall success flag. In single mode, true means the HTTP request completed successfully and the response was processed. In parallel mode, this is true if at least one of the batch requests succeeded, even if others failed.</td><td style="word-wrap: break-word;">True</td></tr>
<tr><td style="word-wrap: break-word;">status_code</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Primary HTTP status code representing the operation. In single mode this is the actual status from the upstream API (e.g., 200, 401, 404, 500). In simplified parallel mode, it is 200 when at least one request succeeded; otherwise 0 on global or parsing failures.</td><td style="word-wrap: break-word;">200</td></tr>
<tr><td style="word-wrap: break-word;">response_headers</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">JSON string encoding metadata about the response. In single mode it is a JSON object of response headers (e.g., Content-Type, Date). In simplified parallel mode it holds a JSON object with an "execution_log" array that lists per-request results and messages rather than raw HTTP headers.</td><td style="word-wrap: break-word;">{"Content-Type": "application/fhir+json;charset=utf-8", "Date": "Mon, 20 Apr 2026 10:05:00 GMT"}</td></tr>
<tr><td style="word-wrap: break-word;">response_time</td><td style="word-wrap: break-word;">FLOAT</td><td style="word-wrap: break-word;">Total elapsed time in seconds for the node run, from before the first request until all work (including retries and all parallel operations) completes.</td><td style="word-wrap: break-word;">1.74</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Performance**: Parallel mode uses a thread pool with up to max_workers threads. Large batches with high max_workers can trigger API rate limits or timeouts; scale concurrency gradually based on upstream service limits.
- **Limitations**: The public interface issues only HTTP GET requests and does not expose request bodies or advanced auth flows such as full OAuth2 or SMART on FHIR. For those kinds of integrations, a more specialized node or service is required.
- **Behavior**: Response format is auto-detected: JSON if the body starts with '{' or '[', XML if it starts with '<', otherwise text. If JSON parsing fails, the raw body is returned and the status_message indicates that JSON was invalid or not parseable.
- **Behavior**: In parallel mode, the success flag is true whenever at least one request is successful, and status_code is set to 200 in that case. Use the execution_log stored inside response_headers to inspect which individual requests failed or timed out.
- **Limitations**: query_params and per-request params in batch_requests must be valid JSON object strings. If they cannot be parsed, the corresponding parameters are skipped and a warning is logged, which may explain missing filters on the remote API.
- **Performance**: Each request uses built-in retry logic with increasing backoff for network errors and server-side 5xx responses, which improves robustness but can lengthen response_time when the upstream service is unstable.

## Troubleshooting
- **Invalid query parameters format**: If logs mention "Invalid query parameters format" and filters are not applied, ensure query_params (and any params in batch_requests) are valid JSON object strings, such as "{\"name\":\"John\", \"_count\":10}", instead of raw query strings like "name=John&_count=10".
- **Batch requests must be a JSON array**: When execution_mode is "parallel" and the node returns "Batch requests must be a JSON array", confirm that batch_requests is a JSON array string (for example, "[{...}, {...}]") rather than a single object or malformed JSON.
- **Network error or timeout**: Errors like "Network error" or messages such as "Request X: TIMEOUT after 30 seconds" indicate connectivity issues or slow upstream APIs. Verify the URL, check that the service is reachable, reduce max_workers, and limit the batch size or query_params to reduce payloads.
- **Unexpected error: ...**: A generic "Unexpected error" usually means invalid input formats or unexpected response structures. Validate batch_requests and query_params JSON using an external tool, test the same URL in single mode, and simplify parameters until the failing element is identified.
- **Empty response_data with non-zero status_code**: If success is false, status_code is non-zero, and response_data is empty, the upstream API likely returned an error status (for example, 401, 403, 404). Check auth_token validity and permissions, confirm preset_endpoint/api_url are correct, and ensure all required query parameters are supplied.
