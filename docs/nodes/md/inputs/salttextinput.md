# Text

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Captures user-provided text and emits it to the workflow while also updating the run UI with the entered value. Supports both single-line and multi-line input and passes the text through unchanged.

</div>
<div style="flex: 0 0 300px;"><img src="../../../images/previews/inputs/salttextinput.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node at the start of a workflow or anywhere you need manual text input (e.g., prompts, configuration strings, messages). Connect its output to downstream nodes that consume strings, such as LLMs, text processors, or formatting utilities.

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
<tr><td style="word-wrap: break-word;">input</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The text to pass into the workflow. Accepts single or multiple lines.</td><td style="word-wrap: break-word;">Summarize the following article...</td></tr>
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
<tr><td style="word-wrap: break-word;">output</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">The text that was entered, passed through unchanged for downstream nodes.</td><td style="word-wrap: break-word;">Summarize the following article...</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **UI output**: In addition to emitting data, this node posts a UI entry showing the submitted text.
- **Multiline supported**: You can enter multi-line text; it is emitted as a single string.
- **Empty behavior**: If no text is provided, the node emits an empty string.
- **Display name**: Appears in the editor as “Text”.
- **Type flexibility**: Output is declared as WILDCARD for broad compatibility; most consumers treat it as a string.

## Troubleshooting
- **No text appears downstream**: Ensure the node’s output is linked to the next node’s input and that the downstream node accepts STRING input.
- **UI panel doesn’t update**: Re-run the workflow and confirm the node is executed; UI updates occur when the node runs.
- **Unexpected empty output**: Verify that the input field isn’t blank; the node intentionally emits an empty string if no text is entered.
- **Formatting lost**: Downstream nodes may alter text; check those nodes if line breaks or spacing appear different.
