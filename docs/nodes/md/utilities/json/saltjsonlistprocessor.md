# JSON List Processor

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

This node takes a JSON list/array string and applies user-provided Python code to each item in parallel via a worker pool. It validates the JSON and the code for safety, executes the transformation with per-item timeouts, and aggregates all results into a JSON string plus an execution log. It is ideal for batch-transforming or enriching records from API responses or other structured JSON datasets.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/utilities/json/saltjsonlistprocessor.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you receive a JSON array and need to run the same transformation logic on every element. Typical workflows include: fetching data upstream with `SaltAPIFetcher` (e.g., lists of patients, observations, or posts), passing its `response_data` into this node to normalize structures, tag records, compute derived fields, or filter content, then sending the `results_json` to downstream analytics, LLM prompting, or storage/export nodes. Within your Python code, you access the current item through `input_data`, its index via `input_index`, and the full list as `all_inputs`, and you define the per-item output by assigning to `result` or returning a value. Configure `max_workers` to balance throughput and resource usage, and set `timeout_seconds` so that slow or stuck items do not block the entire batch. Keep your code pure (no external side effects), rely on simple types for outputs, and ensure a consistent output schema across items so downstream nodes can rely on stable fields.

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
<tr><td style="word-wrap: break-word;">json_list</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">A string containing a JSON-encoded list/array. The top-level value must be a JSON array (e.g., `[ {...}, {...} ]`); if it parses to an object, number, or other type, processing is aborted. Elements can be objects, numbers, strings, or nested structures, but must be JSON-serializable. Very large arrays will increase processing time and memory usage.</td><td style="word-wrap: break-word;">[{"id": 1, "patient_id": "P001", "systolic": 120, "diastolic": 80}, {"id": 2, "patient_id": "P002", "systolic": 140, "diastolic": 90}]</td></tr>
<tr><td style="word-wrap: break-word;">python_code</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Python code executed once per item in the JSON list. You work with `input_data` (current item), `input_index` (its 0-based index), and `all_inputs` (the full list). Set `result` or `output` to define the value returned for that item, or use an explicit `return`. The code is validated to block dangerous operations (e.g., `import os`, `exec`, `eval`, `open`) and must be syntactically correct.</td><td style="word-wrap: break-word;">if isinstance(input_data, dict):     systolic = input_data.get('systolic', 0)     diastolic = input_data.get('diastolic', 0)     risk = 'high' if systolic >= 140 or diastolic >= 90 else 'normal'     result = {         'id': input_data.get('id'),         'patient_id': input_data.get('patient_id'),         'systolic': systolic,         'diastolic': diastolic,         'risk_level': risk,         'index': input_index     } else:     result = {'error': 'unexpected item type', 'index': input_index}  return result</td></tr>
<tr><td style="word-wrap: break-word;">max_workers</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Maximum number of concurrent worker threads used to process list items in parallel. Must be between 1 and 16. Higher values can increase throughput for many small items but may cause more CPU contention; for smaller workloads or heavier per-item logic, lower values may be more stable.</td><td style="word-wrap: break-word;">8</td></tr>
<tr><td style="word-wrap: break-word;">timeout_seconds</td><td>True</td><td style="word-wrap: break-word;">FLOAT</td><td style="word-wrap: break-word;">Maximum execution time in seconds allowed per item. If processing an item exceeds this limit, that item is marked as a timeout, logged as such, and its result becomes null. Overall processing waits for up to approximately `timeout_seconds * len(list)` across all items.</td><td style="word-wrap: break-word;">30.0</td></tr>
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
<tr><td style="word-wrap: break-word;">results_json</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">JSON-formatted string containing the list of per-item results, preserving the original order of input items. Each element is the value produced by your `python_code` for that item (from `result` or returned value), or null where execution failed or timed out. Non-serializable outputs are automatically converted to strings during aggregation.</td><td style="word-wrap: break-word;">[{"id": 1, "patient_id": "P001", "systolic": 120, "diastolic": 80, "risk_level": "normal", "index": 0}, {"id": 2, "patient_id": "P002", "systolic": 140, "diastolic": 90, "risk_level": "high", "index": 1}]</td></tr>
<tr><td style="word-wrap: break-word;">execution_log</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Multiline text log with one entry per item describing its outcome (SUCCESS, ERROR, or TIMEOUT), optionally including captured stdout and stderr from your code. A final summary line reports the total number of items, successes, and failures, useful for auditing and debugging batch behavior.</td><td style="word-wrap: break-word;">Item 0: SUCCESS \| Output: Processed P001 Item 1: SUCCESS \| Output: Processed P002 Processed 2 items. Success: 2, Failed: 0</td></tr>
<tr><td style="word-wrap: break-word;">success_count</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Number of items that were processed successfully, i.e., your code completed without raising an exception or hitting the timeout. This is a quick health indicator for how many records were transformed as intended.</td><td style="word-wrap: break-word;">95</td></tr>
<tr><td style="word-wrap: break-word;">total_items</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Total number of items parsed from the input JSON list. Together with `success_count`, this lets you compute how many items failed (`total_items - success_count`).</td><td style="word-wrap: break-word;">100</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Performance**: Execution uses a ThreadPoolExecutor with up to `max_workers` concurrent tasks. For very large lists, total processing time and memory usage can grow significantly; consider chunking lists upstream if needed.
- **Limitations**: The code validator blocks patterns such as `import os`, `import sys`, `import subprocess`, `open(`, `exec(`, `eval(`, and similar. Any attempt to use these will cause the node to reject the code as unsafe.
- **Behavior**: If `json_list` is invalid JSON or does not represent a top-level array, the node returns an empty results list with an error message and does not attempt per-item execution.
- **Behavior**: If some results are not JSON-serializable (e.g., custom objects), they are converted to strings during aggregation, and a warning about serialization issues is appended to the execution log.
- **Behavior**: Per-item failures (exceptions or timeouts) are isolated: the node continues processing remaining items, logs the error for that index, and inserts null for that result.

## Troubleshooting
- **"Invalid JSON format: ..."**: The `json_list` string cannot be parsed as JSON. Check that it is valid JSON syntax and that characters like newlines or quotes are properly escaped.
- **"Error: Input must be a JSON list/array"**: The JSON parses but the top-level type is not a list. Wrap your records in an array or adjust the upstream node to output a JSON list.
- **"Invalid Python code provided"**: Your code either contains a syntax error or includes blocked patterns (e.g., `import os`, `exec(`, `open(`). Simplify the code, remove unsafe operations, and ensure it parses correctly in a standalone Python file.
- **Log entries like 'Item X: TIMEOUT after N seconds'**: The per-item logic took longer than `timeout_seconds`. Optimize the code for that item, reduce external calls, or cautiously increase the timeout.
- **Log entries like 'Item X: ERROR - ...'**: A runtime exception occurred for that item (e.g., accessing missing keys). Add defensive checks using methods like `input_data.get('field')`, and use prints inside your code to send debug information into the execution log.
