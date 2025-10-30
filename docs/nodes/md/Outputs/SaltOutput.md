# Output

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Finalizes and exports workflow results to disk or UI. Depending on the selected output type, it can save image batches, bundle content into ZIP files, or package an existing path. It also emits UI metadata that references saved assets (filename, subfolder) for downstream consumption.

</div>
<div style="flex: 0 0 300px;"><img src="../../../images/previews/Outputs/SaltOutput.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Place this node at the end of a workflow to persist results. Choose an output_type that matches the data being exported (e.g., PNG/JPEG for image tensors, ZIP for bundling text/images/audio/video, or Path to compress an existing allowed directory/file). The node writes files under a unique subfolder and returns UI metadata pointing to saved assets.

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
<tr><td style="word-wrap: break-word;">output_type</td><td>True</td><td style="word-wrap: break-word;">STRING (one of: STRING, PNG, JPEG, WEBP, ZIP, Path)</td><td style="word-wrap: break-word;">Specifies how to export the provided data. PNG/JPEG save image tensors as files; ZIP bundles various content types; Path zips an existing allowed file/directory; STRING passes through a simple string to the UI.</td><td style="word-wrap: break-word;">PNG</td></tr>
<tr><td style="word-wrap: break-word;">output_data</td><td>True</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">The content to export. Expected types vary by output_type: image tensors for PNG/JPEG, tensors/strings/lists/dicts/bytes for ZIP, a filesystem path for Path, or any value for STRING passthrough.</td><td style="word-wrap: break-word;">Tensor batch of images for PNG/JPEG, or "/abs/path/to/folder" for Path</td></tr>
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
<tr><td style="word-wrap: break-word;">ui</td><td style="word-wrap: break-word;">Not specified</td><td style="word-wrap: break-word;">UI metadata including salt_metadata (reference UUID, file extension, asset flag) and salt_output (list of saved files with filenames and subfolders). For image types, an images array is also included for previews.</td><td style="word-wrap: break-word;">{"ui": {"salt_metadata": [{"salt_id": 12345, "salt_reference_uuid": "<uuid>", "salt_description": "", "salt_asset": true, "salt_file_extension": "PNG"}], "salt_output": [{"filename": "output_12345_0000.png", "subfolder": "<subdir>/<uuid>", "type": "output"}], "images": [{"filename": "output_12345_0000.png", "subfolder": "<subdir>/<uuid>", "type": "output"}]}}</td></tr>
</tbody>
</table>
</div>

## Important Notes
- Output directory structure: files are saved under <output_root>/<output_subdir>/<generated_uuid>/, and UI metadata includes this subfolder so consumers can locate assets.
- PNG/JPEG: Expects a torch tensor (batch). Each image in the batch is saved as a separate file.
- ZIP: Behavior depends on output_data type. Tensors are saved as PNGs into a ZIP; strings are written to a single .txt then zipped; lists are written as multiple .txt files then zipped; dicts are serialized to JSON then zipped.
- ZIP (Audio) and ZIP (Video) handling exists internally for audio bytes and video (image sequence) tensors, but these options are not exposed in the output_type selector shown here.
- Path: Zips the provided file or directory if and only if it resides under allowed roots (input/output/temp). Otherwise, it raises an error.
- STRING: Does not write files; the string is passed through in UI metadata.
- WEBP is listed in previews, but there is no dedicated save branch for WEBP in the current implementation. Selecting WEBP will not save files; it only affects preview handling if results exist.
- Allowed path roots are enforced to prevent unsafe exports. Ensure your paths are under the system's input, output, or temp directories.
- The node is marked as an output node and primarily returns UI metadata rather than typed outputs.

## Troubleshooting
- ZIP with path fails: Ensure the provided path exists and resides within allowed directories (input/output/temp). Otherwise, a validation error is raised.
- No files saved for image output: Confirm output_type is PNG or JPEG and output_data is a valid image tensor batch.
- ZIP produced but empty: Verify output_data matches the expected type for ZIP (e.g., tensor, string, list, dict). Mismatched types result in zips that may be empty or missing expected content.
- Unexpected behavior with WEBP: The current implementation does not save WEBP files. Use PNG or JPEG for file export.
- Cannot find exported files: Use the UI metadata salt_output entries (filename and subfolder) to locate assets on disk under the output directory.

## Example Pipelines

<figure style="margin: 20px 0;">
  <img src="../../../images/assets/Outputs/SaltOutput/Example.png" alt="Example" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" />
  <figcaption style="margin-top: 8px; text-align: center; font-style: italic; color: #666;">Example</figcaption>
</figure>

