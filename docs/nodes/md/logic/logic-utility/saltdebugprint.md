# Debug Print

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

A pass-through debug utility that logs a readable representation of any input value along with a label. It prints container structures and tensor shapes to help inspect workflow data without altering it.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/logic/logic-utility/saltdebugprint.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Insert this node at points in your workflow where you want to inspect intermediate values. Provide the value to inspect and a short label; the node logs a concise representation to the application logs while passing the value forward unchanged, allowing inline debugging.

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
<tr><td style="word-wrap: break-word;">value</td><td>True</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">The value to inspect and log. Accepts any type, including lists, tuples, dicts, numbers, strings, booleans, and tensors.</td><td style="word-wrap: break-word;">42</td></tr>
<tr><td style="word-wrap: break-word;">label</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">A short identifier for the log entry to help find it in logs.</td><td style="word-wrap: break-word;">preprocessor_output</td></tr>
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
<tr><td style="word-wrap: break-word;">value</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">The original input value, passed through unchanged.</td><td style="word-wrap: break-word;">42</td></tr>
</tbody>
</table>
</div>

## Important Notes
- Logging occurs at the DEBUG level; ensure your logging configuration is set to show DEBUG messages to see output.
- Tensor values are summarized by their shape (e.g., Tensor[...]) rather than full contents to keep logs concise.
- Lists, tuples, and dicts are logged structurally; very large or deeply nested data may produce lengthy log lines.
- If an error occurs during formatting, the node still returns the original value and logs an error message.
- Both inputs are required; provide a descriptive label to make log entries searchable.

## Troubleshooting
- No debug output visible: Set the application log level to DEBUG and check the correct log destination.
- Hard to identify entries: Use a unique, descriptive label per debug point.
- Too much output: Place the node sparingly or reduce data size before logging; consider logging only at critical points.
- Tensor contents not shown: This node intentionally logs tensor shapes only; use specialized inspection nodes or save to files if you need full values.
- Custom object appears as type name only: The node logs unknown types by their class name; convert to a basic type or provide a custom string if more detail is needed.
