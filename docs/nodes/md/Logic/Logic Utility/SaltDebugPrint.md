# SaltDebugPrint

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

A debug utility node that logs an input value and its type to the application console, then passes the value through unchanged. It supports complex structures (lists, tuples, dicts) and tensors, summarizing tensors by shape.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/Logic/Logic Utility/SaltDebugPrint.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node inline anywhere you need to inspect the data flowing through a workflow. Insert it between nodes to log intermediate values without altering them. Ideal for verifying shapes, types, or the contents of variables during troubleshooting.

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
<tr><td style="word-wrap: break-word;">value</td><td>True</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">Any value to be logged for debugging. Supports primitives, strings, lists/tuples/dicts (recursively), and tensors.</td><td style="word-wrap: break-word;">[1, 2, 3]</td></tr>
<tr><td style="word-wrap: break-word;">label</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">A short label to identify this log entry in the console output. Can be left empty if no label is desired.</td><td style="word-wrap: break-word;">encoder_output</td></tr>
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
<tr><td style="word-wrap: break-word;">value</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">The original input value, passed through unchanged.</td><td style="word-wrap: break-word;">[1, 2, 3]</td></tr>
</tbody>
</table>
</div>

## Important Notes
- Logging format: values are rendered to a concise debug string; tensors appear as Tensor[shape] (e.g., Tensor[1, 3, 256, 256]).
- Collections (lists, tuples, dicts) are logged recursively; very large structures may produce lengthy logs.
- The node uses debug-level logging. If you do not see output, ensure debug logging is enabled for the application.
- Despite the tooltip calling the label optional, the field is required by the node interface. Use an empty string if you prefer no label.
- Sensitive data caution: this node logs values to the console; avoid passing secrets or private data.

## Troubleshooting
- No log output appears: Ensure the application's log level includes DEBUG and that console logging is enabled.
- Output is too verbose: Reduce collection sizes, pass summaries instead, or avoid logging large objects.
- Only type name is shown for a custom object: The node falls back to the object's class name when it cannot serialize details.
- Unexpected error during logging: The node catches errors internally and still returns the original value; check logs for 'Error in debugtype' or 'Error in debug print' messages.
