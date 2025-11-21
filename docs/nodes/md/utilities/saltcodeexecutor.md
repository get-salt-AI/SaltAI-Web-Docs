# Code Executor

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Executes user-provided Python code against a single input in a controlled environment. Captures stdout/stderr to an execution log and returns the computed result along with success status. The environment restricts dangerous operations and only exposes a safe subset of built-ins and common utility modules.

</div>
<div style="flex: 0 0 300px;"><img src="../../../images/previews/utilities/saltcodeexecutor.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need lightweight, custom transformation or logic that operates on a single piece of data without writing a full custom node. Typical workflows include quick data munging, conditional branching based on computed values, and in-line calculations on text, numbers, or objects passed from prior nodes.

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
<tr><td style="word-wrap: break-word;">python_code</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The Python code to run. Access the incoming value via the variable 'input_data'. You may return a value explicitly with 'return', or assign to a variable named 'result' or 'output'. If none of these are used, the last evaluated expression is used as the result.</td><td style="word-wrap: break-word;"># Access input data data = input_data # Transform and return result = data * 2 if isinstance(data, (int, float)) else str(data).upper() return result</td></tr>
<tr><td style="word-wrap: break-word;">timeout_seconds</td><td>True</td><td style="word-wrap: break-word;">FLOAT</td><td style="word-wrap: break-word;">Suggested timeout duration for the execution. Note: current implementation does not enforce this at runtime.</td><td style="word-wrap: break-word;">10.0</td></tr>
<tr><td style="word-wrap: break-word;">input_data</td><td>False</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">The value to be processed by your Python code. Can be any data type (number, string, object, etc.).</td><td style="word-wrap: break-word;">42</td></tr>
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
<tr><td style="word-wrap: break-word;">result</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">The computed value from your code. Determined by an explicit return, or the variables 'result'/'output', or the last expression's value.</td><td style="word-wrap: break-word;">84</td></tr>
<tr><td style="word-wrap: break-word;">execution_log</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">A human-readable log combining captured stdout and warnings to help debug the code execution.</td><td style="word-wrap: break-word;">Execution completed successfully \| Output: processed </td></tr>
<tr><td style="word-wrap: break-word;">success</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">Indicates whether the code executed without errors.</td><td style="word-wrap: break-word;">true</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Execution model**: If your code contains 'return', it is wrapped and executed to honor the return value. Without 'return', the node returns 'result' or 'output' variables if set, otherwise the last evaluated expression.
- **Restricted environment**: Dangerous patterns are blocked (e.g., 'import os', 'import sys', 'import subprocess', 'import shutil', 'exec(', 'eval(', '__import__', 'open('). Generic import statements are not supported because '__import__' is not exposed.
- **Available modules and built-ins**: Safe built-ins and modules are provided: len, str, int, float, bool, list, dict, tuple, set, min, max, sum, abs, round, sorted, reversed, enumerate, zip, map, filter, range, print, type, isinstance, hasattr, getattr, setattr, and modules json, time, math, random, datetime.
- **Timeout**: The 'timeout_seconds' input is not currently enforced; long-running code can still run until completion or error.
- **Stdout/stderr capture**: print output and warnings are captured and included in 'execution_log'.
- **Input flexibility**: 'input_data' accepts any type; ensure your code defensively handles unexpected types.

## Troubleshooting
- **Invalid Python code or blocked operations**: If 'success' is false with a message like 'Invalid Python code' or references a dangerous pattern, remove disallowed imports/functions and ensure your code parses syntactically.
- **No result returned**: If 'result' is null, ensure you either 'return' a value or assign to 'result' or 'output'. Without these, only the last expression value is used.
- **Import errors**: Standard 'import x' will fail due to missing '__import__'. Use the pre-exposed modules (json, time, math, random, datetime) or avoid imports.
- **Type errors**: If your code assumes a specific type, add checks like 'isinstance(input_data, dict)' and handle alternatives to avoid runtime exceptions.
- **Unexpected long runs**: The 'timeout_seconds' setting is not enforced. Refactor code to avoid blocking calls or heavy loops.
