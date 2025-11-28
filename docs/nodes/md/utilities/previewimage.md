# Preview Image

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Writes incoming images to a temporary preview location so they can be displayed in the UI during workflow execution. It does not produce downstream outputs and is intended purely for visual inspection. Optimized for quick saves and ephemeral storage.

</div>
<div style="flex: 0 0 300px;"><img src="../../../images/previews/utilities/previewimage.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node at the end of an image-processing branch when you want to quickly inspect results without permanently saving them. Typical workflows connect the generated image (e.g., from a decoder, upscaler, or filter node) into Preview Image to monitor intermediate or final outputs during iteration.

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
<tr><td style="word-wrap: break-word;">images</td><td>True</td><td style="word-wrap: break-word;">IMAGE</td><td style="word-wrap: break-word;">The image or image batch to preview. Supports standard IMAGE tensors from upstream nodes.</td><td style="word-wrap: break-word;">IMAGE output from a decoder or upscaler node</td></tr>
</tbody>
</table>
</div>

## Outputs

No outputs

## Important Notes
- This is an output-only preview node: it does not emit data to downstream nodes.
- Previews are stored in a temporary location and may be cleaned up automatically; use a dedicated save node if you need permanent files.
- If you need filenames, folders, or long-term storage, use a save/export node instead of this preview node.
- Category: image

## Troubleshooting
- No preview appears: Ensure the input IMAGE is connected and the upstream node produced a valid image or batch.
- Preview shows only the first image: Some UIs display previews as a scrollable list; verify batch navigation or reduce batch size.
- Metadata not visible: Hidden inputs (prompt, extra_pnginfo) are optional and may be ignored by some UI views.
- Previews disappear between runs: This node uses temporary storage; use a save node to persist results.
