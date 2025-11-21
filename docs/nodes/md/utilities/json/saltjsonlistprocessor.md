# JSON List Processor

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Processes each item in a JSON array using user-provided Python logic, executing items concurrently with a configurable worker pool. Captures execution output and errors per item, aggregates results into a JSON string, and returns a detailed execution log, success count, and total items.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/utilities/json/saltjsonlistprocessor.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you have a JSON list and need to apply the same transformation to each element in parallel. Common in data cleaning or enrichment workflows where each list item (e.g., records from an API) must be processed with custom logic and summarized into a single result set.

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
<tr><td style="word-wrap: break-word;">json_list</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">A JSON-encoded array to process. Each array element is passed to your Python code as input_data.</td><td style="word-wrap: break-word;">[{"id":1,"title":"Item A"},{"id":2,"title":"Item B"}]</td></tr>
<tr><td style="word-wrap: break-word;">python_code</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Python logic applied to each item. Access the current item via input_data and return a value for aggregation. Variables available: input_data (current item), input_index (0-based index), all_inputs (full list), logger.</td><td style="word-wrap: break-word;">A short script that reads fields from input_data and returns a dictionary with computed values.</td></tr>
<tr><td style="word-wrap: break-word;">max_workers</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Maximum number of concurrent worker threads used to process items in parallel.</td><td style="word-wrap: break-word;">4</td></tr>
<tr><td style="word-wrap: break-word;">timeout_seconds</td><td>True</td><td style="word-wrap: break-word;">FLOAT</td><td style="word-wrap: break-word;">Per-item timeout in seconds. If a single item exceeds this duration, it is marked as a timeout and processing continues for others.</td><td style="word-wrap: break-word;">30.0</td></tr>
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
<tr><td style="word-wrap: break-word;">results_json</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">A JSON-encoded array of results returned by your Python logic for each input item. Items that failed or timed out may be null or omitted in the transformation depending on your code.</td><td style="word-wrap: break-word;">[{"post_id":1,"processed":true},{"post_id":2,"processed":true}]</td></tr>
<tr><td style="word-wrap: break-word;">execution_log</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Multi-line text log summarizing the outcome for each item, including any captured stdout/stderr, errors, or timeouts.</td><td style="word-wrap: break-word;">Item 0: SUCCESS \| Item 1: TIMEOUT after 30.0 seconds</td></tr>
<tr><td style="word-wrap: break-word;">success_count</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Number of items that completed successfully according to the node's execution status.</td><td style="word-wrap: break-word;">3</td></tr>
<tr><td style="word-wrap: break-word;">total_items</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Total number of items provided in the input json_list.</td><td style="word-wrap: break-word;">5</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Execution variables**: Your code can use input_data, input_index, all_inputs, and logger.
- **Result handling**: The node collects the value your code returns. If you set a variable named result or output, it will be used; otherwise the node attempts to use the last expression's value.
- **Safety checks**: Code containing high-risk operations (e.g., importing os/sys/subprocess/shutil, using exec/eval/__import__, opening files, prompting for input, or compile) is rejected.
- **Timeouts**: timeout_seconds applies per item. Timed-out items are reported in the log and counted as failures.
- **Serialization**: Results are converted to JSON. Non-serializable values are converted to strings, with a warning appended to the log.
- **Concurrency**: Processing uses a thread pool up to max_workers. Choose a value appropriate for your workload and environment.

## Troubleshooting
- **Invalid JSON input**: If json_list cannot be parsed or is not an array, the node returns an error. Ensure it's a valid JSON array string.
- **Rejected Python code**: If your code uses restricted operations or has syntax errors, it will be rejected. Remove risky imports/functions and fix syntax.
- **Item timeouts**: Increase timeout_seconds if legitimate tasks need more time, or optimize your code for faster execution.
- **Empty results**: If results_json contains nulls or fewer items than total, review your code to ensure it returns a value for each input_data.
- **Serialization errors**: If complex objects are produced, convert them to basic types (dict/list/str/number) before returning to ensure clean JSON output.
- **Low success_count**: Check execution_log for per-item errors or warnings. Validate that your code handles all expected input shapes.
