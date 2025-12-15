# Code Executor

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Runs user-provided Python code against a single input in a restricted, safe environment. Captures stdout/stderr into an execution log and returns the computed result, a log summary, and a success flag. Designed to work with any input type while blocking dangerous operations.

</div>
<div style="flex: 0 0 300px;"><img src="../../../images/previews/utilities/saltcodeexecutor.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need a quick, flexible transformation or computation on data flowing through your workflow. Provide Python code that reads from 'input_data' and either returns a value or assigns it to a variable named 'result' or 'output'. Typical use cases include lightweight data cleaning, formatting, simple math, or conditional logic before passing data to subsequent nodes.

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
<tr><td style="word-wrap: break-word;">python_code</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Python code to execute. Access the incoming value via 'input_data'. The result should be returned, or stored in a variable named 'result' or 'output'.</td><td style="word-wrap: break-word;"># Access input data = input_data # Transform result = data * 2 if isinstance(data, (int, float)) else str(data).upper() return result</td></tr>
<tr><td style="word-wrap: break-word;">timeout_seconds</td><td>True</td><td style="word-wrap: break-word;">FLOAT</td><td style="word-wrap: break-word;">Suggested timeout window for execution in seconds. Keep code short-running.</td><td style="word-wrap: break-word;">10.0</td></tr>
<tr><td style="word-wrap: break-word;">input_data</td><td>False</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">Any input to be processed by the Python code. Available inside your code as 'input_data'.</td><td style="word-wrap: break-word;">5</td></tr>
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
<tr><td style="word-wrap: break-word;">result</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">The computed value from the executed code. If no explicit return is used, the node attempts to use 'result', 'output', or the last evaluated expression.</td><td style="word-wrap: break-word;">10</td></tr>
<tr><td style="word-wrap: break-word;">execution_log</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">A textual summary including captured stdout and any warnings printed during execution.</td><td style="word-wrap: break-word;">Execution completed successfully \| Output: processed OK</td></tr>
<tr><td style="word-wrap: break-word;">success</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">Indicates whether the code executed without errors.</td><td style="word-wrap: break-word;">true</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Safe environment**: Dangerous operations are blocked (e.g., imports like os/sys/subprocess, exec/eval, file I/O, raw input).
- **How to return a value**: Prefer 'return <value>'. If omitted, define 'result' or 'output'; otherwise, the last expression may be used.
- **Available helpers**: Built-ins like len, sum, range, and modules such as json, time, math, random, datetime are exposed.
- **Input flexibility**: 'input_data' can be any type (string, number, dict, list, etc.).
- **Logging**: stdout/stderr are captured and included in 'execution_log'.
- **Timeout behavior**: Keep code short-running. Long-running code may not be forcibly interrupted.
- **No external access**: Avoid network/file system access or module imports—these are intentionally restricted for safety.

## Troubleshooting
- **Invalid Python code or blocked operations**: If 'success' is false and the log mentions dangerous patterns, remove imports (e.g., os, sys) or disallowed functions (exec, eval) and try again.
- **No result returned**: Ensure your code 'return's a value or assigns to 'result' or 'output'.
- **Unexpected result type downstream**: The output type is '*'. If a downstream node expects a specific type, cast or format the result accordingly in your code.
- **Missing prints in log**: Only stdout/stderr printed during execution are captured; ensure you use 'print' within your code if you expect to see messages.
- **Long-running or hanging code**: Simplify logic, remove loops that can hang, and ensure computations finish within the intended time.
