# Code Executor

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Executes custom Python code against a single input value in a restricted, sandbox-like environment. It captures stdout/stderr into a concise execution log and returns the computed result as any data type. Safety checks block dangerous patterns and only a safe subset of built-ins is available.

</div>
<div style="flex: 0 0 300px;"><img src="../../../images/previews/new-uncategorized/SaltCodeExecutor.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need a quick, flexible transformation or computation that isn't covered by existing nodes. Provide input_data (any type) and Python code that reads from input_data and either sets a variable named result or returns a value. The node outputs the computed result, a combined execution log (including any prints), and a success flag.

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
<tr><td style="word-wrap: break-word;">python_code</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Python code snippet to run. Use the variable 'input_data' to access the incoming value. Either set a variable named 'result' (or 'output') or use a return statement.</td><td style="word-wrap: break-word;"># Access input data data = input_data # Example: double numbers or repeat strings result = data * 2 if isinstance(data, (int, float)) else str(data) * 2</td></tr>
<tr><td style="word-wrap: break-word;">timeout_seconds</td><td>True</td><td style="word-wrap: break-word;">FLOAT</td><td style="word-wrap: break-word;">Desired maximum execution time in seconds for the code block.</td><td style="word-wrap: break-word;">10.0</td></tr>
<tr><td style="word-wrap: break-word;">input_data</td><td>False</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">Value passed into your code as 'input_data'. Can be any type (number, string, dict, list, etc.).</td><td style="word-wrap: break-word;">5</td></tr>
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
<tr><td style="word-wrap: break-word;">result</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">The value produced by your code. If you return a value or assign 'result' (or 'output'), that value is emitted. Otherwise, the last evaluated expression may be used.</td><td style="word-wrap: break-word;">10</td></tr>
<tr><td style="word-wrap: break-word;">execution_log</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">A combined message summarizing completion and any printed output or warnings from your code.</td><td style="word-wrap: break-word;">Execution completed successfully \| Output: processed=10</td></tr>
<tr><td style="word-wrap: break-word;">success</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">True if the code executed without errors; False otherwise.</td><td style="word-wrap: break-word;">True</td></tr>
</tbody>
</table>
</div>

## Important Notes
- Code runs in a restricted environment. Dangerous operations (e.g., file I/O, exec/eval, subprocess, direct imports like os/sys, raw input) are blocked.
- Only a safe subset of built-ins is available (e.g., len, str, int, float, bool, list, dict, tuple, set, min, max, sum, abs, round, sorted, enumerate, zip, map, filter, range, print, type, isinstance, hasattr, getattr, setattr, and modules: json, time, math, random, datetime).
- To reliably produce an output, either set a variable named 'result' (or 'output') or use a return statement. If neither is found, the node may fall back to the last evaluated expression.
- The execution_log aggregates printed output and warnings; avoid printing large data to keep logs readable.
- Ensure the downstream nodes can handle the result type you produce (e.g., string vs. number vs. object).

## Troubleshooting
- Invalid Python code: Fix syntax errors and ensure your snippet compiles. The node will report a syntax error in the execution_log.
- Blocked operation detected: Remove disallowed patterns like 'import os', 'exec(', 'eval(', file I/O calls, or subprocess usage.
- Got None as result: Make sure your code returns a value or assigns 'result' (or 'output').
- Unexpected result type: Confirm your code emits the type expected by downstream nodes, or cast/format the result accordingly.
- Empty or missing input_data: If your logic assumes a value, validate input_data and provide defaults to avoid exceptions.
