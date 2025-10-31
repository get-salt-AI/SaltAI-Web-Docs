# Preview Image

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Writes incoming images to a temporary location and exposes them as previews in the Salt UI. It behaves like a lightweight image sink optimized for speed (low compression) and does not persist files long-term.

</div>
<div style="flex: 0 0 300px;"><img src="../../../images/previews/utilities/previewimage.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node at the end of a branch when you want to visually inspect outputs during development or iterative workflows without saving permanent files. It accepts batched images and will produce a preview entry per image in the batch.

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
<tr><td style="word-wrap: break-word;">images</td><td>True</td><td style="word-wrap: break-word;">IMAGE</td><td style="word-wrap: break-word;">One or more images to preview. Supports batched tensors; each batch item will generate a separate preview.</td><td style="word-wrap: break-word;">A tensor of shape [1, 512, 512, 3] from a VAE decode step</td></tr>
</tbody>
</table>
</div>

## Outputs

No outputs

## Important Notes
- **Output node**: This node terminates a branch and does not produce downstream connectors.
- **Temporary storage**: Previews are saved to a temporary directory and may be cleaned up automatically; use Save Image if you need persistence.
- **Batch handling**: For batched inputs, a separate preview is generated for each image in the batch.
- **Performance optimized**: Uses low PNG compression for faster write speed, which can increase file size.
- **Metadata embedding**: When enabled and supplied, prompt and extra metadata are embedded into the preview files.
- **Randomized prefix**: Filenames include a short randomized prefix to avoid collisions across runs.

## Troubleshooting
- **No preview appears**: Ensure the images input is a valid IMAGE tensor (e.g., [B, H, W, 3]) and that the node is executed. Check that the temporary directory is writable.
- **Unexpected number of previews**: Confirm the batch size in the incoming IMAGE tensor; the node writes one preview per batch item.
- **Previews not persistent**: This node is for temporary previews. Use Save Image if you need files to remain available long-term.
- **Missing metadata**: Metadata appears only if provided by the system and metadata saving is enabled in your environment settings.
- **Large images slow to preview**: Very large resolutions can slow down preview generation; consider scaling images before previewing.
