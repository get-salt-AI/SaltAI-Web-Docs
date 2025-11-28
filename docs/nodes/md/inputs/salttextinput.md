# Text

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Provides a user-editable text field for workflows. Supports single-line and multi-line input and emits the entered content to downstream nodes. Useful for prompts, configuration strings, or any free-form text data.

</div>
<div style="flex: 0 0 300px;"><img src="../../../images/previews/inputs/salttextinput.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Place this node at the beginning of a workflow when you need human-provided text. Connect its output to nodes that consume text (e.g., prompt processors, evaluators, message senders). It can also be used to quickly tweak parameters or content without editing node graphs.

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
<tr><td style="word-wrap: break-word;">input</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The text value to pass into the workflow. Accepts multi-line content.</td><td style="word-wrap: break-word;">Write a short, friendly welcome message for new users.</td></tr>
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
<tr><td style="word-wrap: break-word;">output</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">The text value provided by the user, available for any downstream node expecting text or wildcard input.</td><td style="word-wrap: break-word;">Welcome to our platform! Let me know how I can help you get started.</td></tr>
</tbody>
</table>
</div>

## Important Notes
- The input field supports multi-line text; line breaks are preserved in the output.
- If no text is provided, the node outputs an empty string.
- The node’s internal unique_id is auto-assigned and should not be modified.
- Output is a wildcard type, allowing broad compatibility with downstream nodes.

## Troubleshooting
- Text not appearing downstream: Verify the node is connected to the correct input of the next node.
- Unexpected empty output: Ensure the text field is not blank and the workflow run has been updated.
- Line breaks not handled as expected: Confirm the downstream node supports multi-line text and does not trim or sanitize input.
- UI not updating after changes: Save or re-run the workflow to refresh the displayed output.
