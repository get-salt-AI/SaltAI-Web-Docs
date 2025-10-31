# Text

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Provides a simple text input field for workflows, supporting both single-line and multi-line entry. It outputs the entered text as a generic value that can be consumed by any downstream node. Designed to surface the user’s input in the run view as a text message-style entry.

</div>
<div style="flex: 0 0 300px;"><img src="../../../images/previews/inputs/salttextinput.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node whenever you need a user to provide text to your workflow—such as prompts, messages, configuration strings, or parameters. Place it near the start of a workflow and connect its output to nodes that process or act on text.

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
<tr><td style="word-wrap: break-word;">input</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The text to pass through the workflow. Supports multi-line input and is suitable for prompts, messages, or configuration text.</td><td style="word-wrap: break-word;">Write a friendly greeting to a new customer named Alex.</td></tr>
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
<tr><td style="word-wrap: break-word;">output</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">The text value entered by the user, emitted as a generic output so it can connect to any compatible input.</td><td style="word-wrap: break-word;">Hello Alex! Welcome aboard — we're excited to have you with us.</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Multiline supported**: The input field allows multi-line text.
- **Generic output type**: The output is wildcard-typed, making it broadly compatible with other nodes expecting text.
- **Empty handling**: If no text is provided, the node outputs an empty string.
- **Display name**: Appears in the editor as "Text" under the SALT/Inputs category.

## Troubleshooting
- **Nothing appears downstream**: Ensure the Text node’s output is connected to the correct input on the next node.
- **Unexpected empty output**: Confirm that the input field is not blank and that any upstream overrides are disabled.
- **Cannot find the node**: Look for it under the SALT/Inputs category with the display name "Text".
