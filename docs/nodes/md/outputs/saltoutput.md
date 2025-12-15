# Output

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Finalizes and exports workflow results. Depending on the selected output type and the data provided, it saves files (e.g., images or archives) to the Salt output directory and emits a UI payload describing the saved artifacts for display and download.

</div>
<div style="flex: 0 0 300px;"><img src="../../../images/previews/outputs/saltoutput.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this as the terminal node to persist and surface results. Connect the data you want to export (images, text, JSON, or a filesystem path) and choose the appropriate output type. The node writes files under an auto-generated unique subfolder and returns UI metadata for the Salt interface.

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
<tr><td style="word-wrap: break-word;">output_type</td><td>True</td><td style="word-wrap: break-word;">STRING (enum: STRING \| PNG \| JPEG \| WEBP \| ZIP \| Path)</td><td style="word-wrap: break-word;">Select how the output should be handled: STRING returns plain text in the UI; PNG/JPEG save image batches as files; ZIP creates a .zip (supports zipping image batches or textual/JSON content); Path zips a provided file or directory.</td><td style="word-wrap: break-word;">PNG</td></tr>
<tr><td style="word-wrap: break-word;">output_data</td><td>True</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">The data to export. Supported combinations: PNG/JPEG require a torch tensor batch of images; ZIP supports a tensor batch (images as PNG inside the ZIP), a string (saved as .txt), a list (each item saved as numbered .txt), or a dict (saved as .json); Path requires a valid path string within allowed Salt directories (input/output/temp). STRING will display the value directly in the UI.</td><td style="word-wrap: break-word;">A tensor batch of images for PNG/JPEG, or a string like "Process complete" for STRING, or a dict like {"scores": [0.9, 0.8]} for ZIP</td></tr>
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
<tr><td style="word-wrap: break-word;">ui</td><td style="word-wrap: break-word;">Not specified</td><td style="word-wrap: break-word;">Emits a UI payload with salt_metadata and salt_output entries describing saved files (filenames, subfolder, type) and, for image types, a preview list. This node has no downstream data outputs.</td><td style="word-wrap: break-word;">{"ui": {"salt_metadata": [{"salt_reference_uuid": "<uuid>", "salt_asset": true, "salt_file_extension": "PNG"}], "salt_output": [{"filename": "output_1_0000.png", "subfolder": "<uuid>", "type": "output"}], "images": [{"filename": "output_1_0000.png", "subfolder": "<uuid>", "type": "output"}]}}</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Image saving**: PNG/JPEG only save when output_data is a tensor batch of images. Each image in the batch is saved as a separate file.
- **ZIP behavior**: ZIP supports zipping (a) image batches (as PNG files), (b) a single string to a .txt, (c) a list of items to numbered .txt files, or (d) a dict to a single .json.
- **Path zipping**: Path zips an existing file or directory but only if it resides within Salt's allowed directories (input, output, or temp); otherwise an error is raised.
- **STRING mode**: STRING does not write files; it surfaces the provided text directly in the UI.
- **Output location**: Files are written under the Salt output directory into an auto-generated unique subfolder (UUID). The UI response includes filenames and the subfolder for retrieval.
- **WEBP selection**: While WEBP is available as a selectable type, saving behavior is implemented for PNG/JPEG images. Choose PNG or JPEG when exporting image tensors.
- **No downstream outputs**: This node is an output node; it returns UI metadata only and does not pass data to subsequent nodes.

## Troubleshooting
- **No files saved for images**: Ensure output_type is PNG or JPEG and output_data is a valid image tensor batch.
- **ZIP created but contents unexpected**: Verify output_data matches the intended ZIP mode (tensor for images, string/list/dict for text/JSON).
- **Path error: not allowed**: Move the file/directory under the Salt input/output/temp directories or update your selection to a permitted location.
- **Empty UI preview**: Image previews appear only when output_type is PNG or JPEG and files were actually saved.
- **Selecting WEBP yields no files**: Use PNG or JPEG for image saving; WEBP is not currently saved by this node.

## Example Pipelines

<figure style="margin: 20px 0;">
  <img src="../../../images/assets/outputs/saltoutput/example.png" alt="Example" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" />
  <figcaption style="margin-top: 8px; text-align: center; font-style: italic; color: #666;">Example</figcaption>
</figure>

