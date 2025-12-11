# Text

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Provides a simple text input for Salt workflows. It accepts user-entered text (single-line or multi-line) and emits it downstream while also updating the workflow UI with the latest value.

</div>
<div style="flex: 0 0 300px;"><img src="../../../images/previews/inputs/salttextinput.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node whenever you need to feed arbitrary text into a workflow—such as prompts, messages, configuration strings, or any text for downstream processing. Place it at the beginning of a workflow or anywhere a user-editable string is required. The node emits the raw text and updates the UI panel to reflect the current value.

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
<tr><td style="word-wrap: break-word;">input</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The text to pass through the workflow. Supports multi-line input. No dynamic prompt expansion is applied.</td><td style="word-wrap: break-word;">Summarize the following text...</td></tr>
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
<tr><td style="word-wrap: break-word;">output</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">The provided text value, passed through as-is for downstream nodes.</td><td style="word-wrap: break-word;">Hello, Salt!</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **UI behavior**: The node posts its current text value to the UI under a text-type entry for visibility and auditing.
- **Multiline**: Input supports multi-line content; useful for longer prompts or blocks of text.
- **Default value**: If no text is provided (or input is null), it emits an empty string.
- **Type compatibility**: Output is marked as wildcard (*) to connect broadly, but the actual content is string text.
- **No dynamic prompts**: Dynamic prompt expansion is disabled for this input.

## Troubleshooting
- **Empty output**: If downstream nodes receive an empty value, ensure the 'input' field contains the expected text (the node emits an empty string by default if none is provided).
- **UI not reflecting changes**: Reconnect or re-execute the node to refresh the UI. The internal unique_id is used for UI updates; avoid manually altering it.
- **Connection issues**: If a downstream node rejects the connection, confirm it can handle string inputs even though the output type is wildcard.
