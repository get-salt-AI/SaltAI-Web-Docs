# SaltDebugPrint

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Utility node that logs a readable representation of the input value and its type to the debug console, tagged with a user-provided label. The input value is passed through unchanged, allowing inline inspection within workflows.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/logic/logic-utility/saltdebugprint.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node to inspect data flowing through your workflow at specific points. Connect any value to 'value' and provide a short 'label' to identify the log entry. The node will log the value’s structure/type and forward the value so downstream nodes continue to operate normally.

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
<tr><td style="word-wrap: break-word;">value</td><td>True</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">The value to be inspected and logged. Supports arbitrary types including numbers, strings, lists, tuples, dicts, and tensors.</td><td style="word-wrap: break-word;">42</td></tr>
<tr><td style="word-wrap: break-word;">label</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">A short label to identify this debug output in logs.</td><td style="word-wrap: break-word;">After normalization</td></tr>
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
<tr><td style="word-wrap: break-word;">value</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">The original input value, unchanged, allowing continued use in the workflow.</td><td style="word-wrap: break-word;">42</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Pass-through behavior**: The node never modifies the input value; it only logs and returns it.
- **Logging level**: Output is written at debug level. If debug logging is disabled, you may not see messages.
- **Representation details**: Containers (lists, tuples, dicts) are logged recursively; strings are quoted; basic scalars are printed directly; tensors are summarized by shape (e.g., Tensor[1, 3, 256, 256]) rather than full contents.
- **Label usage**: The label appears in the log prefix to help identify where the message originated.
- **Error handling**: If logging or formatting fails, the node still returns the original value.

## Troubleshooting
- **No output visible**: Ensure the system's debug logging is enabled. Without debug level logging, messages will not appear.
- **Logs are too verbose**: Reduce usage of this node or tighten logging level to info/warn in your environment.
- **Large or complex values slow logging**: Logging very large nested structures can be slow. Consider sampling or placing fewer debug points.
- **Cannot distinguish multiple logs**: Use unique, descriptive labels per placement to make entries easy to identify.
