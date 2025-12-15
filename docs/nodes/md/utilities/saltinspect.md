# Inspect

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Inspects and displays any value flowing through a workflow. It formats text/JSON for readability, summarizes tensors, and attempts to render an image preview (including decoding base64-encoded images). Optionally writes a detailed summary to the system logs.

</div>
<div style="flex: 0 0 300px;"><img src="../../../images/previews/utilities/saltinspect.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node anywhere you need to debug or understand intermediate data. Connect any upstream node output to Inspect to view a human-readable summary, pretty-printed JSON (when applicable), and an image preview if the value is an image tensor or a base64-encoded image.

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
<tr><td style="word-wrap: break-word;">value</td><td>True</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">Any value to inspect (e.g., strings, numbers, dicts, lists, JSON strings, torch tensors, or objects). JSON strings are detected and pretty-printed; tensors are summarized; base64 image strings are decoded for preview when possible.</td><td style="word-wrap: break-word;">{"user": "alice", "score": 42}</td></tr>
<tr><td style="word-wrap: break-word;">write_logs</td><td>True</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">If true, writes a formatted summary of the value (with separators) to the logs.</td><td style="word-wrap: break-word;">true</td></tr>
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
<tr><td style="word-wrap: break-word;">value</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The inspected value as a string. For JSON inputs, this will be pretty-printed. For other types, a readable string representation is provided.</td><td style="word-wrap: break-word;">{   "user": "alice",   "score": 42 }</td></tr>
<tr><td style="word-wrap: break-word;">image_preview</td><td style="word-wrap: break-word;">IMAGE</td><td style="word-wrap: break-word;">An image tensor preview derived from the input, when possible. If the input is an image tensor or a base64 image string, a corresponding preview is produced; otherwise, a default blank image is returned.</td><td style="word-wrap: break-word;">Tensor with shape [1, H, W, 3] and float32 values in [0, 1]</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Output Node**: This node is designed to display information directly in the UI and can act as a terminal inspection point in a workflow.
- **JSON handling**: If the input is a valid JSON string, it is parsed and pretty-printed; otherwise the raw string is shown.
- **Tensor handling**: Tensors are summarized in text and, when they represent images, are shown as an image preview.
- **Base64 images**: Strings starting with data URL prefixes (e.g., "data:image/..."), JPEG markers ("/9j/"), or PNG markers ("iVBOR") are treated as base64 images and decoded for preview.
- **Fallback preview**: If an image cannot be derived from the input, a small blank image is returned as a preview.
- **Logging control**: Set write_logs to false to avoid verbose logs when inspecting large or frequent values.

## Troubleshooting
- **Image preview shows a blank image**: Ensure the input is either an image-like tensor (with valid dimensions and channels) or a valid base64-encoded image string (e.g., "data:image/png;base64,<base64-data>").
- **JSON did not pretty-print**: Verify the input is valid JSON. If you pass a Python-like string (single quotes or non-JSON syntax), the node will display it as a raw string. Prefer passing a dict/list object or a valid JSON string.
- **Large outputs slow down logging**: Disable write_logs to prevent heavy log I/O when inspecting large tensors or long strings.
- **Unexpected text in the node display**: The UI shows a stringified version of the result. If the input is complex or includes non-serializable objects, provide a simpler representation (e.g., convert to JSON-serializable structures).

## Example Pipelines

<figure style="margin: 20px 0;">
  <img src="../../../images/assets/utilities/saltinspect/example.png" alt="Example" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" />
  <figcaption style="margin-top: 8px; text-align: center; font-style: italic; color: #666;">Example</figcaption>
</figure>

