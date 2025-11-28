# JSON List Processor

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Processes each element of a JSON list concurrently using user-provided Python logic. It validates code for safety, executes with a per-item timeout, captures outputs/warnings, and aggregates results into a JSON string along with an execution log. Designed for scalable per-item transformations with configurable worker count.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/utilities/json/saltjsonlistprocessor.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you have a JSON array (e.g., from an API) and you need to run the same Python transformation on each item in parallel. Typical workflow: fetch or build a JSON list, pass it here with concise Python code that uses 'input_data' to produce a result per item, then consume the aggregated 'results_json' and review 'execution_log'.

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
<tr><td style="word-wrap: break-word;">json_list</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">A JSON-encoded array of items to process. Must be valid JSON and an array type.</td><td style="word-wrap: break-word;">[{"id":1,"title":"Example"},{"id":2,"title":"Another"}]</td></tr>
<tr><td style="word-wrap: break-word;">python_code</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Python code executed for each item. Use 'input_data' (current item), 'input_index' (0-based), 'all_inputs' (the full list), and 'logger' for logging. Must return a value (e.g., dict) per item.</td><td style="word-wrap: break-word;">Python code that reads 'input_data' and returns a transformed dictionary</td></tr>
<tr><td style="word-wrap: break-word;">max_workers</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Maximum number of concurrent workers used to process items in parallel.</td><td style="word-wrap: break-word;">4</td></tr>
<tr><td style="word-wrap: break-word;">timeout_seconds</td><td>True</td><td style="word-wrap: break-word;">FLOAT</td><td style="word-wrap: break-word;">Timeout for each item execution (seconds). Items exceeding this timeout are marked as timed out.</td><td style="word-wrap: break-word;">30.0</td></tr>
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
<tr><td style="word-wrap: break-word;">results_json</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">JSON-encoded array of results returned by your code for each processed item. Non-serializable results are converted to strings with a warning noted in the log.</td><td style="word-wrap: break-word;">[{"post_id":1,"processed":true},{"post_id":2,"processed":true}]</td></tr>
<tr><td style="word-wrap: break-word;">execution_log</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Concatenated log lines for each item (success, warnings, errors, or timeouts) plus a summary.</td><td style="word-wrap: break-word;">Item 0: SUCCESS \| Item 1: SUCCESS \| Processed 2 items. Success: 2, Failed: 0</td></tr>
<tr><td style="word-wrap: break-word;">success_count</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Number of items that executed successfully.</td><td style="word-wrap: break-word;">2</td></tr>
<tr><td style="word-wrap: break-word;">total_items</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Total number of items in the input list.</td><td style="word-wrap: break-word;">2</td></tr>
</tbody>
</table>
</div>

## Important Notes
- Code safety restrictions apply: dangerous patterns such as imports of system modules, exec/eval, file I/O, and similar are blocked. Invalid patterns or syntax will cause validation failure.
- The node runs items in parallel. Result ordering follows completion order, not necessarily the original input order.
- Per-item timeout applies; total collection waits up to timeout_seconds × number_of_items.
- If results contain non-JSON-serializable types, they are converted to strings and a warning is appended to the execution log.
- Provide a valid JSON array in 'json_list'. Objects or strings that are not arrays will be rejected.
- Available variables inside your code: 'input_data', 'input_index', 'all_inputs', and 'logger'. Ensure your code returns a result for each item.

## Troubleshooting
- Error: Invalid JSON format — Ensure 'json_list' is valid JSON and is an array. Validate the JSON with a linter if needed.
- Error: Invalid Python code provided — Remove restricted operations (e.g., imports of os/sys/subprocess, exec/eval, file I/O) and fix syntax errors. Keep code minimal and self-contained.
- Items timing out — Increase 'timeout_seconds' or reduce workload per item. Also consider lowering 'max_workers' if the environment is resource-constrained.
- Empty list provided — Supply a non-empty JSON array; otherwise, the node returns empty results with an informational log.
- Unexpected non-serializable results — Ensure your code returns JSON-serializable values (e.g., dict/list/str/int/float/bool) to avoid string coercion and warnings.
- Results appear out of order — This is expected due to parallel execution. If order matters, include 'input_index' in your result and sort downstream.
