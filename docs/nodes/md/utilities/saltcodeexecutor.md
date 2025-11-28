# Code Executor

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Executes user-provided Python code against a single input value and returns the result. It runs in a constrained environment with a curated set of safe built-ins, captures stdout/stderr to an execution log, and provides a success flag for easy branching.

</div>
<div style="flex: 0 0 300px;"><img src="../../../images/previews/utilities/saltcodeexecutor.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node to quickly transform or validate data with simple Python expressions. Typical workflows include lightweight preprocessing, mapping an input to another type, formatting values, or prototyping logic between nodes. Provide the input via the 'input_data' port (any type) and a short Python snippet that reads from 'input_data' and sets a result.

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
<tr><td style="word-wrap: break-word;">python_code</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Python code to execute. Read the incoming value from 'input_data' and produce a result by either assigning to 'result' or using a return statement.</td><td style="word-wrap: break-word;">result = input_data if input_data is not None else 'empty'</td></tr>
<tr><td style="word-wrap: break-word;">timeout_seconds</td><td>True</td><td style="word-wrap: break-word;">FLOAT</td><td style="word-wrap: break-word;">Maximum time allocated for the code execution in seconds. Keep your code short-running.</td><td style="word-wrap: break-word;">10.0</td></tr>
<tr><td style="word-wrap: break-word;">input_data</td><td>False</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">The value to be processed by your Python code. Can be any type (string, number, boolean, list, dict, etc.).</td><td style="word-wrap: break-word;">42</td></tr>
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
<tr><td style="word-wrap: break-word;">result</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">The computed value from the executed code. Type depends on your code (can be any type).</td><td style="word-wrap: break-word;">84</td></tr>
<tr><td style="word-wrap: break-word;">execution_log</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Collected stdout/stderr messages and a short status line indicating whether the execution completed successfully.</td><td style="word-wrap: break-word;">Execution completed successfully \| Output: processed</td></tr>
<tr><td style="word-wrap: break-word;">success</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">True if code executed without errors; False otherwise.</td><td style="word-wrap: break-word;">true</td></tr>
</tbody>
</table>
</div>

## Important Notes
- Your code should reference the input via 'input_data' and set an output by assigning to 'result' or by using a return statement.
- A restricted set of safe built-ins is available (common types, math/random/datetime modules, basic functions). Access to OS/file/network/process execution and dynamic evaluation is blocked.
- Potentially dangerous patterns are disallowed (e.g., import os/sys/subprocess/shutil, exec, eval, __import__, open, input, compile). Such code will be rejected as invalid.
- Keep code short-running and deterministic. Avoid infinite loops or heavy computations to prevent timeouts.
- The node captures print output and warnings; these appear in 'execution_log' for debugging.

## Troubleshooting
- Invalid Python code: If 'success' is false and the log mentions 'Invalid Python code', check syntax and remove any disallowed functions/imports.
- No result returned: Ensure your code assigns to 'result' or uses a 'return' statement. If neither is present, the output may not be what you expect.
- Type errors at runtime: Verify that 'input_data' is the type your code expects. Add type checks (e.g., isinstance) to handle mixed inputs.
- Empty or None output: Confirm your logic path sets a value for all conditions. Initialize 'result' to a sensible default.
- Timeouts or long runs: Simplify the code, avoid large loops, and lower data sizes to keep execution within the specified time.
