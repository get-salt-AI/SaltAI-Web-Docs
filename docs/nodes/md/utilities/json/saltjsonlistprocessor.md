# JSON List Processor

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Processes each item of a JSON array concurrently using user-provided Python logic. It captures outputs and warnings per item, aggregates results into a JSON string, and reports a structured execution log with success and failure counts. Designed for fast, parallel transformations of list-based JSON data.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/utilities/json/saltjsonlistprocessor.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you have a JSON array (e.g., from an API or a previous node) and need to apply the same transformation to each item. Provide the JSON list and a Python snippet that reads the current item via 'input_data' and sets a result. Configure worker count for concurrency and set a per-item timeout. Typical workflow: fetch or generate JSON list -> JSON List Processor -> downstream analysis or storage.

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
<tr><td style="word-wrap: break-word;">json_list</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">A JSON-formatted array of items to process. Must be a valid JSON list (top-level array). Each element is passed to the code as 'input_data'.</td><td style="word-wrap: break-word;">[{"id":1,"title":"Example"},{"id":2,"title":"Another"}]</td></tr>
<tr><td style="word-wrap: break-word;">python_code</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Python code that processes a single item. Access the current item via 'input_data' and set 'result' (or return a value) to produce the output for that item. Variables available: input_data, input_index, all_inputs, logger.</td><td style="word-wrap: break-word;"># Use 'input_data' and set 'result' accordingly</td></tr>
<tr><td style="word-wrap: break-word;">max_workers</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Number of concurrent workers used to process items in parallel.</td><td style="word-wrap: break-word;">4</td></tr>
<tr><td style="word-wrap: break-word;">timeout_seconds</td><td>True</td><td style="word-wrap: break-word;">FLOAT</td><td style="word-wrap: break-word;">Maximum allowed execution time per item. Items exceeding this timeout are marked as timed out.</td><td style="word-wrap: break-word;">30.0</td></tr>
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
<tr><td style="word-wrap: break-word;">results_json</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">A JSON string representing a list of per-item results in the order tasks complete. Items that failed or timed out may be null depending on error handling.</td><td style="word-wrap: break-word;">[{"processed":true}, {"processed":true}, null]</td></tr>
<tr><td style="word-wrap: break-word;">execution_log</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">A newline-separated log of per-item execution outcomes, including stdout, warnings, errors, and a final summary.</td><td style="word-wrap: break-word;">Item 0: SUCCESS \| Output: ...\nItem 1: ERROR - ...\nProcessed 10 items. Success: 9, Failed: 1</td></tr>
<tr><td style="word-wrap: break-word;">success_count</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Number of items that executed successfully.</td><td style="word-wrap: break-word;">9</td></tr>
<tr><td style="word-wrap: break-word;">total_items</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Total number of items provided in the input JSON list.</td><td style="word-wrap: break-word;">10</td></tr>
</tbody>
</table>
</div>

## Important Notes
- The input must be a valid JSON array (top-level list). Objects or invalid JSON will be rejected.
- The code runs in a restricted environment. Disallowed patterns include: import os, import sys, import subprocess, import shutil, exec(, eval(, __import__, open(, file(, input(, raw_input(, compile(). If detected, the code is rejected.
- Available variables in your code: input_data (current item), input_index (0-based), all_inputs (the entire list), logger (for logging).
- You can set 'result' (or return a value) in the code to produce the per-item output.
- Execution is concurrent using multiple workers. The 'results_json' list reflects completion order, not the original input order.
- Per-item stdout and stderr are captured and summarized in the execution log.
- Per-item timeout applies. Timed-out items are logged as TIMEOUT and produce a null result.
- If a result cannot be serialized to JSON, it is converted to a string with a warning appended to the execution log.

## Troubleshooting
- Invalid JSON format: Ensure 'json_list' is a valid JSON array string. If you see 'Invalid JSON format', validate the JSON and remove trailing commas or malformed entries.
- Input is not an array: Supply a top-level JSON list (e.g., [{...}, {...}]). Objects like {"a":1} are not accepted.
- Invalid Python code provided: Remove disallowed patterns and fix syntax errors. Keep logic self-contained and avoid imports that are blocked.
- Item timeouts: Increase 'timeout_seconds' or reduce workload per item if you see 'TIMEOUT' messages.
- Unexpected nulls in results: Items that failed or timed out produce null. Review 'execution_log' for the specific item’s error or exception details.
- Serialization warnings: If complex objects appear in results, convert them to primitive types or strings in your code to avoid JSON serialization issues.
