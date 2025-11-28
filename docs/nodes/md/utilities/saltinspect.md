# Inspect

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Inspects any value flowing through a workflow, formats it for readability, and optionally writes a structured log entry. It also generates a best-effort image preview from tensors or embedded/base64 image data and shows the inspected text directly on the node UI.

</div>
<div style="flex: 0 0 300px;"><img src="../../../images/previews/utilities/saltinspect.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node to debug and understand intermediate data as it moves through a workflow. Connect any output to its input to view a readable summary, log it (if enabled), and, when possible, see a preview image. Commonly placed after model calls, data transformations, or image-producing steps to verify structure and content.

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
<tr><td style="word-wrap: break-word;">value</td><td>True</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">The value to inspect. Accepts any type, including strings, JSON-like structures, tensors, objects with an 'image' tensor attribute, or base64-encoded image strings (data URLs or raw base64 for JPEG/PNG).</td><td style="word-wrap: break-word;">{"result": {"label": "cat", "confidence": 0.92}}</td></tr>
<tr><td style="word-wrap: break-word;">write_logs</td><td>True</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">If true, writes a formatted inspection block to the server logs.</td><td style="word-wrap: break-word;">true</td></tr>
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
<tr><td style="word-wrap: break-word;">value</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">A formatted string representation of the inspected input. JSON is prettified when detected; non-JSON strings are returned as-is; other structures are serialized to JSON-like text.</td><td style="word-wrap: break-word;">{   "result": {     "label": "cat",     "confidence": 0.92   } }</td></tr>
<tr><td style="word-wrap: break-word;">image_preview</td><td style="word-wrap: break-word;">IMAGE</td><td style="word-wrap: break-word;">An image tensor preview derived from the input when possible. If the input is an image-like tensor or contains base64 image data, a preview is produced; otherwise a blank image is returned.</td><td style="word-wrap: break-word;">Tensor of shape [1, 64, 64, 3] float32 in [0, 1]</td></tr>
</tbody>
</table>
</div>

## Important Notes
- The node displays the inspected text directly in its UI panel after execution.
- If the input is a tensor with image-like shape, it is used directly for the preview. Otherwise, the node attempts to decode base64 image data from strings or JSON fields.
- Unsupported or unrecognized image inputs yield a default blank 64×64 RGB image.
- The 'value' output is always a string (not the original object). If you need the original data downstream, branch the graph before this node.
- Write logs toggles server-side logging of a formatted inspection block for easier debugging.
- The node accepts objects with an 'image' attribute that is a tensor; that tensor will be used for the preview.

## Troubleshooting
- Preview shows a black/blank square: ensure the input is an image-like tensor (shape [B,H,W,C], [H,W,C], or [C,H,W]) or a valid base64-encoded image (data URL or raw JPEG/PNG base64).
- Text shows as a raw string or '[object Object]'-like content: provide valid JSON strings or JSON-serializable structures for clearer formatting.
- No text appears in the node UI: confirm the node executed successfully and that upstream nodes produced data; also check that logging is not required for UI display.
- Unexpected tensor preview shape: ensure tensors are in image-compatible layouts with channel count 1, 3, or 4.

## Example Pipelines

<figure style="margin: 20px 0;">
  <img src="../../../images/assets/utilities/saltinspect/example.png" alt="Example" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" />
  <figcaption style="margin-top: 8px; text-align: center; font-style: italic; color: #666;">Example</figcaption>
</figure>

