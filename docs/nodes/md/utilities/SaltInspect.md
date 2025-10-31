# Inspect

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Inspects any value flowing through your workflow. It logs a readable summary, shows the value as text on the node UI, and attempts to produce an image preview when possible (e.g., tensors or base64-encoded images).

</div>
<div style="flex: 0 0 300px;"><img src="../../../images/previews/utilities/SaltInspect.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node to debug and understand intermediate data. Place it after any node whose output you want to inspect. It will format text/JSON for readability, log details (optional), and generate an image preview from tensors or base64 image data.

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
<tr><td style="word-wrap: break-word;">value</td><td>True</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">The value to inspect. Accepts any type, including strings, JSON-like data, tensors, or objects that stringify. If the value is a tensor image or contains base64-encoded image data, a preview image will be generated.</td><td style="word-wrap: break-word;">{"image":"data:image/png;base64,iVBOR..."}</td></tr>
<tr><td style="word-wrap: break-word;">write_logs</td><td>True</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">When true, writes a formatted inspection block to the logs. Disable to avoid console/log clutter for large or frequent outputs.</td><td style="word-wrap: break-word;">true</td></tr>
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
<tr><td style="word-wrap: break-word;">value</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">A formatted string representation of the inspected input. JSON is preserved and pretty-printed when detected.</td><td style="word-wrap: break-word;">{   "a": 1,   "b": "text" }</td></tr>
<tr><td style="word-wrap: break-word;">image_preview</td><td style="word-wrap: break-word;">IMAGE</td><td style="word-wrap: break-word;">An image tensor preview when available. Derived from input tensors or base64-encoded image data; otherwise a blank placeholder image.</td><td style="word-wrap: break-word;">Tensor shaped like [1, H, W, C] with C in {1,3,4}</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Formatting behavior**: The output 'value' is always a STRING. Non-string inputs are serialized; strings that contain valid JSON are parsed and re-serialized for readability, while regular strings are passed through unchanged.
- **Image preview detection**: The node attempts to render an image from: (a) image-like tensors, or (b) base64-encoded images found directly in the input string or inside JSON (e.g., data:image/... or raw base64 beginning with /9j/ for JPEG or iVBOR for PNG).
- **Tensor handling**: If the input is a tensor shaped like an image, it will be used directly as the preview; otherwise a default blank preview is returned.
- **Logging control**: Set write_logs=false to avoid writing to logs, useful for high-frequency or large payloads.
- **UI display**: The node shows the inspected text in an on-node read-only text area to facilitate quick review.
- **Category**: SALT/Utility

## Troubleshooting
- **Blank image preview**: Ensure the input is an image-like tensor ([B,H,W,C] or [H,W,C]) or includes a valid base64 image string (e.g., starting with data:image,..., /9j/ for JPEG, or iVBOR for PNG).
- **Unexpected stringification**: The 'value' output is a string for inspection, not the original typed object. If you need to pass the original type forward, branch your workflow before Inspect.
- **Logs missing**: Set write_logs=true to enable logging. Verify logging configuration if nothing appears.
- **Unreadable or compact JSON**: Provide a valid JSON string or an object that serializes to JSON; invalid JSON will be treated as a plain string.
- **Performance with large data**: For very large tensors or long strings, disable write_logs to reduce log overhead; previews fall back to a small blank image when decoding fails.

## Example Pipelines

<figure style="margin: 20px 0;">
  <img src="../../../images/assets/utilities/SaltInspect/Example.png" alt="Example" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" />
  <figcaption style="margin-top: 8px; text-align: center; font-style: italic; color: #666;">Example</figcaption>
</figure>

