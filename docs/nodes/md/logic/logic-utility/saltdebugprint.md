# Debug Print

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

A debugging utility node that logs an input value and its type/structure to the console, then passes the value through unchanged. It provides concise representations for complex data (e.g., tensors show shape, containers are summarized).

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/logic/logic-utility/saltdebugprint.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node anywhere in a workflow to inspect intermediate data without altering it. Insert SaltDebugPrint between nodes, give it a clear label, and check the console/log output to verify values, shapes, and structures during troubleshooting or development.

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
<tr><td style="word-wrap: break-word;">value</td><td>True</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">Any value to be inspected and logged. Supports scalars, strings, dicts, lists, tuples, tensors, and other objects.</td><td style="word-wrap: break-word;">['cat', 'dog', 'bird']</td></tr>
<tr><td style="word-wrap: break-word;">label</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">A short label to identify this debug print in the logs.</td><td style="word-wrap: break-word;">embedding_check</td></tr>
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
<tr><td style="word-wrap: break-word;">value</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">The original input value, passed through unchanged for continued processing.</td><td style="word-wrap: break-word;">['cat', 'dog', 'bird']</td></tr>
</tbody>
</table>
</div>

## Important Notes
- The node logs at debug level; ensure your logging configuration shows debug messages to see output.
- Tensors are summarized as Tensor[shape] rather than printing full contents.
- Lists, tuples, and dicts are printed recursively in a compact representation; very large structures may still produce lengthy logs.
- Even if logging fails, the node returns the original value unchanged.
- The label input is required and is included in log messages to help identify the source.

## Troubleshooting
- No output appears in console/logs: Ensure debug-level logging is enabled and the runtime log view is open.
- Output is too verbose: Place the node only where needed or reduce the size of structures being logged.
- Need full tensor values: Convert tensors to smaller summaries (e.g., stats) upstream before passing into this node, since it prints only shapes.
- Label missing or unclear: Provide a unique, descriptive label to distinguish multiple debug prints in the same workflow.
