# JSON List Processor

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Processes each item in a JSON array concurrently using user-provided Python logic. You supply a JSON list and a short processing script that operates on each item as input_data; the node runs items in parallel with a configurable worker count and per-item timeout. Returns aggregated results as a JSON string along with an execution log, success count, and total item count.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/utilities/json/saltjsonlistprocessor.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you have a JSON array (e.g., API response or precomputed list) and need to transform or analyze each element in parallel. Typical workflow: provide the JSON list, write simple Python logic that reads input_data and produces a result object, set max_workers and timeout, then run. The node aggregates per-item outputs into a JSON array and logs per-item execution details.

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
<tr><td style="word-wrap: break-word;">json_list</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">A JSON-formatted list (array) of items to process. Each element is passed as input_data to your Python logic.</td><td style="word-wrap: break-word;">[{"id":1,"title":"First"},{"id":2,"title":"Second"}]</td></tr>
<tr><td style="word-wrap: break-word;">python_code</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Python logic that runs for each element. Use the variable input_data (and optionally input_index, all_inputs) to read the current item and set a result (e.g., a dict or value) to be collected.</td><td style="word-wrap: break-word;">Use input_data to compute and assign a result value for each item.</td></tr>
<tr><td style="word-wrap: break-word;">max_workers</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Maximum number of concurrent workers used to process items in parallel.</td><td style="word-wrap: break-word;">4</td></tr>
<tr><td style="word-wrap: break-word;">timeout_seconds</td><td>True</td><td style="word-wrap: break-word;">FLOAT</td><td style="word-wrap: break-word;">Per-item execution timeout in seconds. Items exceeding this timeout are marked as timed out.</td><td style="word-wrap: break-word;">30.0</td></tr>
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
<tr><td style="word-wrap: break-word;">results_json</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Aggregated results as a JSON string array. Each element corresponds to the output from processing one input item.</td><td style="word-wrap: break-word;">[{"post_id":1,"processed":true},{"post_id":2,"processed":true}]</td></tr>
<tr><td style="word-wrap: break-word;">execution_log</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Multiline log summarizing per-item execution outcomes, including success messages, warnings, and timeouts.</td><td style="word-wrap: break-word;">Item 0: SUCCESS \| Item 1: SUCCESS \| Processed 2 items. Success: 2, Failed: 0</td></tr>
<tr><td style="word-wrap: break-word;">success_count</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Number of items processed successfully.</td><td style="word-wrap: break-word;">2</td></tr>
<tr><td style="word-wrap: break-word;">total_items</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Total number of items provided in the input JSON list.</td><td style="word-wrap: break-word;">2</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Safety checks**: The node validates code and rejects potentially dangerous patterns (e.g., attempts to run system commands or import sensitive modules).
- **Execution variables**: Your code can reference input_data, input_index, all_inputs, and logger.
- **Per-item timeout**: timeout_seconds applies to each item; timed-out items are logged and counted as failures.
- **Parallelism**: max_workers controls concurrency; higher values can improve throughput but increase resource usage.
- **Serialization**: Results are returned as a JSON string. Non-serializable objects are converted to strings when necessary.
- **Empty or invalid input**: An empty list returns no results; invalid JSON input returns an error in the log with zero successes.

## Troubleshooting
- **Invalid JSON list**: If you see 'Invalid JSON format' or 'Input must be a JSON list/array', ensure the json_list is a valid JSON array (starts with [ and contains properly formatted items).
- **Code rejected as unsafe**: If you get 'Invalid Python code provided', remove disallowed patterns (e.g., system imports, exec/eval) and keep logic focused on transforming input_data.
- **Syntax errors in code**: Fix Python syntax issues (missing colons, indentation, etc.) before running.
- **Per-item timeout**: If logs show 'TIMEOUT', increase timeout_seconds or reduce the complexity of the per-item logic.
- **Result not JSON-serializable**: If the log warns about serialization, ensure your result is a JSON-friendly type (dict, list, string, number, boolean, or null).
- **Unexpected None results**: Ensure your code sets a result value for each item using the available variables.
