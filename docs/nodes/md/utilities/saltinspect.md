# Inspect

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

SaltInspect taps into any point of a workflow to show what data looks like at runtime. It formats and optionally logs a human-readable summary, with special handling for JSON, tensors, and base64 images, and returns a string plus an image preview tensor. The node also exposes the inspected text to the canvas UI, making it a practical debugging and monitoring tool.

</div>
<div style="flex: 0 0 300px;"><img src="../../../images/previews/utilities/saltinspect.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use SaltInspect whenever you need to understand or debug intermediate data flowing through a workflow. Common scenarios include inspecting JSON responses from APIs, checking model outputs (such as tensors or generated text), or verifying that earlier nodes are producing the expected structures and values before feeding them into stricter processors.

Typically, you place SaltInspect downstream of nodes like SaltInput, SaltTextInput, integration or tool nodes, model inference nodes, or table/JSON transformers to observe what they output. Upstream, connect any node that produces data of any type (the input is WILDCARD). Downstream, you can route the formatted string to text processing, logging, or storage nodes, and the image tensor to image‑capable consumers such as display, export, or image processing nodes. It fits especially well alongside nodes like SaltDisplayAny for richer inspection of final outputs or nodes that enforce schemas.

Best practice is to temporarily add SaltInspect at critical pipeline checkpoints (API responses, model outputs, schema transformations), use the on‑node UI preview to validate behavior, and then either keep it with write_logs disabled to avoid log noise or remove it once the pipeline is stable. When working with binary or image data encoded as base64, SaltInspect will attempt to decode a preview image, making it easier to verify that encoded assets are valid and correctly formatted.

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
<tr><td style="word-wrap: break-word;">value</td><td>True</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">The value to inspect. Accepts any type: strings, numbers, dictionaries, lists, tensors, and arbitrary objects. Strings that are valid JSON are parsed and pretty‑printed. Tensors are summarized with shape, dtype, and device, and may produce a direct image preview if they look like images. Base64‑encoded image strings (including data:image/... URLs and common JPEG or PNG prefixes such as "/9j/" and "iVBOR") are recognized and decoded for preview when possible.</td><td style="word-wrap: break-word;">{"user_id": 42, "status": "ok", "images": [{"thumb": "data:image/png;base64,iVBORw0KGgoAAA..."}]}</td></tr>
<tr><td style="word-wrap: break-word;">write_logs</td><td>True</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">Whether to write a detailed inspection message to the server logs. When true, the node logs a formatted block with separators and the inspected content. Disable this in high‑volume or production workflows to avoid excessive logging or leaking sensitive data into logs.</td><td style="word-wrap: break-word;">true</td></tr>
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
<tr><td style="word-wrap: break-word;">value</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">A string representation of the inspected value. If the original value is valid JSON (string or structured), it is serialized using the JSONFormatter for consistent, pretty‑printed output; otherwise it is the original string or a JSON‑like dump of the object. This is suitable as input to downstream text nodes, serializers, or logging and storage nodes.</td><td style="word-wrap: break-word;">{"user_id": 42, "status": "ok", "images": [{"thumb": "data:image/png;base64,iVBORw0KGgoAAA..."}]}</td></tr>
<tr><td style="word-wrap: break-word;">image_preview</td><td style="word-wrap: break-word;">IMAGE</td><td style="word-wrap: break-word;">An image tensor representing a preview of the inspected data, when possible. If the input is an image‑like tensor (shape similar to [B,H,W,C], [H,W,C], or [C,H,W] with 1, 3, or 4 channels), that tensor is used or converted to a batched image. If the input contains a recognizable base64‑encoded image (including data URLs and common JPEG or PNG prefixes), it is decoded and converted into a [1,H,W,3] float32 tensor in the range [0,1]. If no image can be derived, a default 1×64×64×3 black image tensor is returned.</td><td style="word-wrap: break-word;">An RGB preview tensor with logical shape [1, 256, 256, 3] representing a single decoded image that downstream display or image‑processing nodes can consume.</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Performance**: Inspecting large tensors or deeply nested JSON structures can be expensive to stringify and log; consider disabling write_logs or using SaltInspect only at key checkpoints in high‑throughput workflows.
- **Limitations**: Base64 image detection relies on common prefixes such as data:image, "/9j/" for JPEG, and "iVBOR" for PNG; non‑standard encodings or truncated data may fail to decode and will fall back to a blank preview image.
- **Behavior**: JSON‑like strings are parsed and re‑serialized, which can change whitespace and key ordering (depending on JSONFormatter), so the logged or returned JSON may not exactly match the original string formatting.
- **Behavior**: When the input is a tensor that is not clearly image‑shaped (for example, model weights or embeddings), it is not visualized as an image; instead, SaltInspect returns a default 64×64 preview image and logs a textual tensor summary.

## Troubleshooting
- **Inspection shows 'Error during inspection: ...'**: This indicates an exception while formatting or parsing the data (such as invalid UTF‑8 or a problematic string representation). Check the upstream node for malformed data, or temporarily simplify the value by inspecting a smaller subset or a simpler structure.
- **Image preview is just a small black square**: The node could not interpret the input as an image (no recognized base64 image or non‑image tensor shape). Verify that the input is a valid image tensor or a correctly prefixed base64 string, for example starting with "data:image/png;base64," or "/9j/".
- **Logs are extremely verbose or contain sensitive content**: Disable the write_logs input or remove SaltInspect from sensitive sections. Only keep logging enabled where the additional visibility is necessary and data is safe to store in logs.
- **Tensor summary is shown but there is no meaningful image**: The tensor shape or channel order may not match expected image formats. Ensure image tensors are in one of the supported layouts (such as [B,H,W,C] or [H,W,C] with 1, 3, or 4 channels) before connecting them to SaltInspect if you require a valid visual preview.

## Example Pipelines

<figure style="margin: 20px 0;">
  <img src="../../../images/assets/utilities/saltinspect/example.png" alt="Example" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" />
  <figcaption style="margin-top: 8px; text-align: center; font-style: italic; color: #666;">Example</figcaption>
</figure>

